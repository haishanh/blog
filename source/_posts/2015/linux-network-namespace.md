title: Network namespace挺有趣
date: 2015-11-03 21:57:14
tags:
---
今天才发现`ip netns`是这么强大。`ip netns`操作的就是Linux network namespace。

namespaces, cgroups这些技术是Linux LXC的基础，所以也是现在流行的Docker的基础。对于namespeces，有process的namespace，也有network的namespace。如果一个进程跑在一个process namespace中，那么它只能看到同在这个namespace中的进程，也只能和这些进程通信。你在这个namespace中`ps -elf`一下，你也只能看到跑在这个namespace的进程。

<!-- more -->

Linux的network namespace也是一样的道理，只是它是用于隔离网络。每个network namespace中有独立的网络设备，有自己独立的`lo`。每个network namespace中也有自己独立的路由表，iptables规则等。结合veth，利用network namespace可以很方便的在主机配置虚拟网络。具体什么是network namespace可以[man 8 ip-netns][ip-netns-man]看看。

## A simple example

为了方便,我们不使用Linux bridge或者OVS，只在主机中配置一个点到点的简单网络。

首先创建2个namespace

```
ip netns add ns1
ip netns add ns2
```

此时`ls /var/run/netns`会看到2个文件`ns1`和`ns2`。这2个文件打开后产生的fd指向对应的namespace。

创建veth **pair**

```
ip link add name tap1 type veth peer name tap2
```

这时，在主机上就会多出2个interface tap1和tap2。然后把这2个interface分别加到ns1和ns2中

```
ip link set tap1 netns ns1
ip link set tap2 netns ns2
```

这时，主机上原来多出的2个interface又会消失。此后，如果我们要操作这些interface的话，必须要加上`ip netns exec <ns-id>`前缀，比如现在把这2个interface配上地址并起起来。

```
ip netns exec ns1 ip addr add 2.2.2.1/24 dev tap1
ip netns exec ns1 ip link set tap1 up

ip netns exec ns2 ip addr add 2.2.2.2/24 dev tap2
ip netns exec ns2 ip link set tap2 up
```

这个时候，你就可以尝试 

```
ip netns exec ns1 ping 2.2.2.2
```

没有意外的，应该是可以ping通的。你也可以在ns2的对应interface上抓包

```
ip netns exec ns2 tcpdump -i tap2
```

如果你觉得输入前面的`ip netns exec <ns-id>`很麻烦，你也可以在这个namespace中运行xterm。当然这需要你通过GUI登录机器，或者进行x-window转发。

```
ip netns exec ns1 xterm &
```

上面的命令会打开一个新的terminal。`ip link list`一下，你应该就能看到`lo`和`tap1`了。

## How pipework works with Docker containter

如果你尝试过让Docker container和外部通信，你可能用过[jpetazzo/pipework][pipework]。pipework是一个简单的用来给LXC container配置网络的Bash脚本。

pipework使用起来通常是这样(当然还有很多其他用法)：

```
pipework br0 904bcf846a3e 10.10.10.9/24
```

其中`br0`是bridge名，`904bcf846a3e`是container id，`10.10.10.9/24`会配到这个container中新创建的interface eth0上。然后你把主机上的物理interface加到这个`br0`中，这个container就可以和外部网络通信了。pipework用的其实就是上面例子中的那些工具。

pipework首先会根据第一参数，在此是`br0`，去检查主机上有没有这个bridge，如果有的话，会判断是Linux bridge还是OVS bridge。如果主机上不存在，pipework会创建这个bridge，pipework也会根据这个参数去猜测用户是想创建Linux bridge还是OVS bridge。

然后pipework会根据这个container id获得这个container的pid。假设这个pid是`14806`，则`/proc/14806/ns/net`就是这个container真正的network namespace文件对象。pipework会在/var/run/netns/下创建一个指向这个文件的软链接。

```
ln -s /proc/14806/ns/net /var/run/netns/14806
```

此时我们运行`ip netns list`时，就可以看到`14806`这个namespace。之后pipework会创建一个veth pair。大概是这样：

```
ip link add name veth1pl14806 mtu 1500 type veth peer name veth1pg14806 mtu 1500
```

其中mtu的具体值，实际中是直接使用`br0`的mtu值。`veth1pl14806`就是'v'加上container中要创建的interface名字，加上'p'，加上'l'(应该是local的意思)，在再加上container的pid。`veth1pg14806`同理，'l'换成'g'(guest)。当然，这个命名不重要，只要唯一就可以了。

之后，pipework会把`veth1pl14806`加到`br0`中。

```
## if br0 is linux bridge
ip link set veth1pl14806 master br0 || brctl addif br0 veth1pl14806
## if br0 is OVS bridge
ovs-vsctl add-port br0 veth1pl14806
## bring it up
ip link set veth1pl14806 up
```

把`veth1pg14806`加到`14806`这个namespace中。然后改名成eth1，再配置`10.10.10.9/24`这个地址和相应路由。


```
ip link set veth1pg14806 netns 14806
ip netns exec 14806 ip link set veth1pg14806 name eth1
ip netns exec 14806 ip add add 10.10.10.9/24 dev eth1
...
```

最后会删除之前创建的软链接`/var/run/netns/14806`，来防止用户通过`ip netns`来做更改。


[ip-netns-man]: http://man7.org/linux/man-pages/man8/ip-netns.8.html
[pipework]: https://github.com/jpetazzo/pipework
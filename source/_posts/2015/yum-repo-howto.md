title: 自己搞一个Yum源
date: 2015-11-06 23:21:47
tags:
---
[无root权限从源码编译安装很痛苦][build-from-source]，就算有root权限安装个软件什么的也很麻烦。而且公司的机器又不能访问外网。所以我不如自己搭建一个源镜像，这样大家都可以用。正好实验室有配置比较低的闲置机器，配置一下代理就可以了。由于我们Team基本用的都是Redhat Enterprise Linux或者Cent OS，而且基本上都是7.0+，所以搭建的是EL7的yum源，应该也可以用于其他Enterprise Linux系列distro。

<!-- more -->

首先检查机器上是否有`reposync`和`createrepo`。前者用于从远端repo中拉取rpm包到本地，后者用于创建repo生产metadata等。没有的话，可以通过yum安装。

```
yum install yum-utils createrepo
```

然后可以写一个，如下的脚本：

```bash
#!/bin/bash

repos="base extras updates"
for repo in $repos; do
  reposync -a x86_64 -r $repo -p /opt/mirror/el/7/
  createrepo "/opt/mirror/el/7/$repo/"
done
```

其中`repos`中是你希望拉取到本地的repo的id。在`/etc/yum.repos.d/`下面的repo文件一般都是如下的形式：

```
[one]
name=Repo One
baseurl=http://example.com/el7/one/
enabled=1
gpgcheck=1
gpgkey=file:///etc/one/

[two]
name=Repo Two
baseurl=http://example.com/el7/two/
enabled=1
gpgcheck=0
```

其中`one`和`two`就是repo id。虽然说是id，但其实可以有多个文件存在相同的repo id。

前面的脚本跑一次一般需要比较长的时间，当然这个要看网络情况。接下来，我们跑一个web server，这里选择nginx。使用yum默认安装的话，nginx使用的默认配置文件应该是`/etc/nginx/nginx.conf`，添加以下行：


```
...
http {
    ...
    server {
        ...
        location /el/7/ {
        root   /opt/mirror;
        autoindex  on; 
        }
        ...
    }
}
```

运行nginx

```
systemctl start nginx
```

假设主机的IP是1.2.3.4，此时我们访问`http://1.2.3.4/el/7/`就应该能看到东西了。

我们提供给别人的repo文件，应该是这样：

```
[base]
name=Test Repo base
baseurl=http://1.2.3.4/el/7/base/
enabled=1
gpgcheck=0

[extras]
name=Test Repo extras
baseurl=http://1.2.3.4/el/7/extras/
enabled=1
gpgcheck=0

[updates]
name=Test Repo updates
baseurl=http://1.2.3.4/el/7/updates/
enabled=1
gpgcheck=0
```

我只是公司内网使用，就不管什么gpgkey了。gpgcheck全设为0。

如果我们还有点*情怀*，我们还应该提供一个mirror使用的帮助页面，比如[网易CentOS镜像使用帮助][netease-mirror]。用markdown写个，转成html，存到`/opt/mirror/el/7/help/index.html`就可以了。

[build-from-source]: /2015/build-and-install-from-source-with-out-root/
[netease-mirror]: http://mirrors.163.com/.help/centos.html



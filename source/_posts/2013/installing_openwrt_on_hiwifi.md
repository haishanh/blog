date: 2013-12-13 18:58:46
title:  Installing OpenWRT on Hiwifi
---

买了个极路由，外观什么的确实挺酷，管理界面也比较友好，但所谓的App Store加速什么的，我真的完全没体会到，而且我的笔记本连上后，隔一段时间就会掉。所以就准备刷个OpenWRT，而且还可以用用goagent，之前尝试过一次，刷机应该是成功了，但telnet的时候一直连不上，应该是因为极路由地址是192.168.199.1的[问题](http://www.xj123.info/4251.html)，但当时不知道。前天晚上又重新搞了一下，基本是搞定了。

可能你需用用到的一些工具和文件，我提供在此，每个文件后面的注释中有注明出处，感谢各位。
点[这儿](http://pan.baidu.com/s/1w3sBD)下载,百度网盘。  
>
File1: HiWiFi_OpenWrt.zip //极路由论坛上的官方人员提供的OpenWRT固件 [出处](http://bbs.hiwifi.com/thread-7710-1-1.html)
File2: 小极修砖工具.rar //极路由论坛上的官方人员提供的修复工具 出处同上
File3: recovery.bin //巢鹏编译好的固件 [出处](http://chaopeng.me/blog/2013/10/28/hiwifi.html)
File4: gevent_1.0rc2-1_ar71xx.ipk //适用于ar71xx平台已编译好的gevent [出处](http://www.right.com.cn/forum/thread-121599-1-1.html)
File5: python-greenlet_0.4.0-1_ar71xx.ipk //处理多线程 出处同上





<!--more-->

-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+

[TOC]


##编译Hiwifi的OpenWRT固件
上一次尝试刷的时候，我是自己编译的，但编译时间比较长，这次我是直接使用的是巢鹏编译好的固件，也就是*File3*: recovery.bin，如果你适用这个文件的话，可以直接跳到刷机步骤
以下编译方法都是从[carabob](http://bbs.hiwifi.com/thread-7475-1-1.html)，[巢鹏](http://chaopeng.me/blog/2013/10/28/hiwifi.html)，[Hacker007](http://blog.omitol.com/openwrt-for-hiwifi/)等人那抄过来的，我就是做个记录。
###环境准备
理论上有个Linux就可以了，但推荐用Ubuntu 12.04+，再安装subversion、git和build-essential等
    
    sudo apt-get install build-essential
    sudo apt-get install subversion git
###获取源码

    svn co https://code.hiwifi.com/svn/hiwifi
遇到提示输入密码时，直接打回车，然后出来输入用户名的提示，输入极客社区帐号email，再输入密码。
###编译
    cd hiwifi/trunk
    ./scripts/feeds update -a
    ./scripts/feeds install -a
    make package/symlinks
使用默认的编译选项
    
    make HC6361_defconfig
也可以自定义编译选项
    
    make menuconfig
可以把openssh、python、pythonopenssl、unbound等，都配置进去。

最后：

    make -j4
如果出错，要查看详细的编译信息可以使用V=s参数:

    make -j4 V=s
开始漫长的编译。
###制作成recovery.bin
    wget -O rom.bin http://updaterom.ikcd.net/upgrade_file/HC6361-0.775.784s_130802-131633-96d56f0c
    dd if=rom.bin of=uboot.bin bs=1k count=128
    cat uboot.bin bin/ar71xx/openwrt-ar71xx-generic-tw150v1-squashfs-sysupgrade.bin >recovery.bin
如果运行第一条命令时，上面的链接404，暂时的解决方法是跳过wget，手动下载官方的一个[recovery.bin](http://bbs.hiwifi.com/thread-7710-1-1.html),也可以解压*File1*： HiWiFi_OpenWrt.zip得到，重命名为rom.bin然后执行下面的命令提取uboot.bin。
###刷机
 1. 解压*File1*： HiWiFi_OpenWrt.zip后的文件夹里有tftpd程序(.exe)，将上一步生成的recovery.bin文件放入tftpd程序所在目录，替换掉原来的recovery.bin
2. 用网线将极路由 LAN 口与电脑网口相连
3. 将电脑网络接口 IP 设置为 192.168.1.88/255.255.255.0，如果不行的话就设置成192.168.199.88/255.255.255.0，具体我忘了。
4. 电脑上运行tftp程序，断开极路由电源，用尖锐物按住极路由 RESET 不放，再接上极路由电源
5. 等待电脑上 tftpd 出现传输 recovery.bin 进度条完成后，松开 RESET
6. 极路由面板灯进入跑马灯状态，跑完后，系统自动重启，刷机完成
7. 如果刷机失败的话，用我压缩包里的小极修砖工具进行修复，具体方法看[这里](http://bbs.hiwifi.com/thread-7492-1-1.html)。

##OpenWRT配置
这部分内容来自[ninehills](https://gist.github.com/ninehills/2627163)
###初始配置
[第一次登陆](http://wiki.openwrt.org/zh-cn/doc/howto/firstlogin)OpenWRT路由器需要用telnet登陆你的路由，Windows下可能需要[安装](https://www.google.com.hk/search?q=windows+telnet&oq=windows+telnet&aqs=chrome..69i57j69i65l3j69i60j69i61.3599j0j7&bmbp=1&sourceid=chrome&espvd=215&es_sm=93&ie=UTF-8)一下telnet，可以在cmd下试一下
然后将路由器的Lan口和你电脑相连，电脑上设置为DHCP模式（如果不行的话，就按刚才刷机中的第3步配置）。然后 

    telent 192.168.199.1  //此处极路由应是192.168.199.1，其他大部分路由应是192.168.1.1

成功后出现OpenWrt的欢迎界面类似这样：

```
BusyBox v1.17.3 (2011-02-22 23:42:42 CET) built-in shell (ash)
Enter 'help' for a list of built-in commands.
  _______                     ________        __
 |       |.-----.-----.-----.|  |  |  |.----.|  |_
 |   -   ||  _  |  -__|     ||  |  |  ||   _||   _|
 |_______||   __|_____|__|__||________||__|  |____|
          |__| W I R E L E S S   F R E E D O M
 ATTITUDE ADJUSTMENT (bleeding edge, r26290) ----------
  * 1/4 oz Vodka      Pour all ingredents into mixing
  * 1/4 oz Gin        tin with ice, strain into glass.
  * 1/4 oz Amaretto
  * 1/4 oz Triple sec
  * 1/4 oz Peach schnapps
  * 1/4 oz Sour mix
  * 1 splash Cranberry juice
 -----------------------------------------------------
root@openwrt:~$
 ```
然后更改root密码：

```
root@openwrt:~$ passwd root
Changing password for root
New password:
Retype password:
Password for root changed by root
root@openwrt:~$
```
更改root密码后dropbear(SSH 服务)就运行了，输入`exit`退出telent，然后

    ssh root@192.168.199.1
以后就可以通过ssh管理OpenWrt.
###网络配置
LuCI是OpenWrt的web管理界面（Web UI），如果你编译固件时没有将LuCI配置进去的话，你就需要先在命令行下完成一定的网络配置，你当然也可以一直用命令行，而不安装LuCI
首先备份相关配置，防止出错：

    cp /etc/config ~/ -r
然后用vi修改相关配置。 首先修改`/etc/config/wireless`文件，注释掉

    # option disabled 1
配置文件中，在一行语句的前面加`#`来注释这行语句，使该语句作用失效
然后修改`/etc/config/network`文件，首先修改*lan*接口配置，注释掉此行：

    # option ifname 'eth0'
然后增加*wan*接口，如果你上级网络是DHCP的，则文件的末尾添加：
```
config interface 'wan'
    option ifname 'eth0'
    option proto 'dhcp'
```
如果你上级网络是静态IP，则在文件的末尾添加类似如下的语句：
```
config interface 'wan'
    option ifname 'eth0'
    option proto 'static'
    option ipaddr '10.22.33.124'
    option netmask '255.255.255.0'
    option gateway '10.22.33.1'
    option dns '202.113.16.10'
```
然后将路由器的Lan/Wan口接到上级网络中，重启路由器。这时便可以通过电脑寻找SSID为 *OpenWrt*的无线网络，加入后便可以通过：

    ssh root@192.168.199.1
来连接路由器。此时路由也工作在路由模式下，可以正常上网了。
无线网络的加密需要修改`/etc/config/wireless`文件，配置wpa加密需要修改`config wifi-iface`段

    option ssid OpenWrt
    option encryption psk2
    option key        'secret passphrase'
###安装软件
OpenWrt上安装软件需要用opkg包管理系统，主要命令：
```
# 查看帮助
opkg help
# 更新数据库，必做
opkg update
# 列出已安装的包
opkg list-installed
# 安装LuCI
opkg install luci
# 更多 参见 http://wiki.openwrt.org/doc/howto/luci.essentials
```
如果你也用的是巢鹏编译好的固件的话，你可能也会在`opkg update`时，出现`HTTP`错误，这是更新源链接无效的问题，可以修改`/etc/opkg.conf`这个文件，把下面这行：

    src/gz attitude_adjustment http://xxxx.whatever.xxx/yyy/zzz dest root / dest ram /tmp lists_dir ext /var/opkg-lists option overlay_root /overlay
中的这个链接改成`http://downloads.openwrt.org/attitude_adjustment/12.09/ar71xx/generic/packages/`，其他部分不用变动

##安装goagent
这部分前提是你已经会使用goagent了
这部分内容参考[这里](http://www.openwrt.org.cn/bbs/forum.php?mod=viewthread&tid=14500)，感谢网友mw4530r
###安装python等
安装python的时间可能稍长
```
opkg update
# 安装 python
opkg install python
# 安装 pyopenssl python-openssl libopenssl libevent2
opkg install pyopenssl python-openssl libopenssl libevent2
```
###把goagent传至路由器
下载GoAgent, 解压文件到电脑上
修改GoAgent的local文件夹里的proxy.ini文件

将GoAgent 监听 ip改成0.0.0.0
```
[listen]
ip=0.0.0.0
port=8087
```
将appid改成我之前已经在Google App Engine 部署好的appid, 多个appid用“|”分隔
```
[gae]
appid = idone|idtwo|idthree
```
另外，将PAC设定 `enable = 0`
```
[pac]
enable = 0
```
修改GoAgent的local文件夹里的proxy.pac文件，把里面的127.0.0.1:8087全部替换成路由器的内网IP，如 192.168.199.1:8087
创建目录/app/goagent/local/certs
    
    mkdir -p /app/goagent/local/certs

把GoAgent的local文件夹里的 certs目录, proxy.py, 及被更改过的 proxy.ini 复制到路由器/app/goagent/local目录下，将被更改过的 proxy.pac 复制到路由器/www目录下
```
scp goagent_dir/local/certs/* root@192.168.199.1:/app/goagent/local/certs
scp goagent_dir/local/proxy.py root@192.168.199.1:/app/goagent/local
scp goagent_dir/local/proxy.pac root@192.168.199.1:/www
```
###安装gevent
gevent可以让goagent稳定运行，gevent可以自己编译，或者直接使用现成的`gevent_1.0rc2-1_ar71xx.ipk`,`python-greenlet_0.4.0-1_ar71xx.ipk`，注意只有*ar71xx平台*可以使用，bcm等其他平台还是要自己编译。同过跟上面类似的scp命令，上传至路由器，比如上传到`/tmp`目下，进行安装：
```
opkg install /tmp/python-greenlet_0.4.0-1_ar71xx.ipk
opkg install /tmp/gevent_1.0rc2-1_ar71xx.ipk
```
###运行goagent
    
    python /app/goagent/local/proxy.py

这时在连了该路由器的电脑上使用如下代理，就可以至少访问[Newyork Times](http://www.nytimes.com/)这类非`https`链接的网站了，访问`https`网站，你还需要导入GoAgent证书
```
HTTP proxy: http://192.168.199.1
Port: 8087
```

###GoAgent证书导入
注: 我目前使用的是GoAgent 3.1.0，在导入了证书以后，用iOS 上的Safari访问Twitter还是不行，访问Facebook和Youtube却没问题，我也看到很多和我一样的情况，后期应该会有修复吧

Windows和Linux应该分别用管理员和root权限在本地运行一下GoAgent就可以到导入GoAgent证书了，不行的话，这样操作：
Windows下Chrome中
> 设置->显示高级设置->HTTPS/SSL->管理证书->受信任的根证书颁发机构->导入->下一步,浏览->local\CA.crt->下一步->将所有的证书都放入下列存储->受信任的根证书颁发机构->下一步,完成

Linux下Chrome中
> 设置->显示高级设置->HTTPS/SSL->管理证书->授权中心->导入->下一步,浏览->local\CA.crt->把复选框都选中->确定

Firefox中，*我不知道，你可以Google的嘛*
iOS平台，通过sarari访问 [https://goagent.googlecode.com/files/CA.crt](https://goagent.googlecode.com/files/CA.crt)这里，然后安装，你也可以吧`CA.crt`通过邮件的方式发给自己，但你需要用iOS自带的邮件应用打开附件。

###使用PAC文件作为自动代理设置
如下：
    
    http://192.168.199.1/proxy.pac
注意：`http://192.168.199.1`后面不需要接端口。除非你的`proxy.ini`中的pac是enable的
iOS平台下：
> Settings -> Wi-Fi -> CurrentWiFi -> HTTP Proxy -> Auto -> http://192.168.199.1/proxy.pac

###开机启动GoAgent
修改`/etc/rc.local`，在`exit 0`之前增加以下命令
    
    python /app/goagent/local/proxy.py >/dev/null 2>&1 &
    
-EOF-

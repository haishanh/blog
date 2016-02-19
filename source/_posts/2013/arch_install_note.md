date: 2013-08-12 17:32:27
title: Arch安装小记
---

![archlinux-log](http://7fva40.com1.z0.glb.clouddn.com/archlinux-logo.png)

在Windows上用VirtualBox尝试了[Arch Linux][arch]，在体验一段时间就立马就喜欢上了这个系统。AUR的存在，使得安装软件方便了太多，原来ubuntu时，安装个软件总是会有这样那样的问题。任何你想安装的软件xxx，你都可以在Arch上用`yaourt -S xxx`来查找并安装。如果你喜欢自己编译安装，在Arch上你也完全有这个自由。当然了，Arch的优点还有很多，但上面这一点，就足以让我产生替换ubuntu的冲动。

原来笔记本上安装的时ubuntu，于是就寻思着利用周末的时候安装一下Arch。Arch有这非常详尽的wiki，中文wiki也很全，[Beginners' Guide][beginners_guide]上有详细的安装说明，而且大部分的操作都给出相关的解释以及其他的选项，所以安装起来并不困难。当然还是比安装ubuntu要复杂多了，毕竟ubuntu的安装过程是GUI的。对于我来说最大的问题是网络的问题，折腾了很久很久（真的是很久==）才搞定，所以这篇文章对于我来说也是个备忘，下次安装的时候可以参考。

<!--more-->

安装时的Live环境下，在我的笔记本上不需任何操作就可以联网，但每次安装完reboot后，进入系统就怎么也连不上网，`dhcpcd`的操作也无效，`ip link` 连无线网卡都看不到，只有有线网卡。好吧第一次也许是我的姿势不对，于是我又安装了第二次，这次仔细看了Beginners' Guide里关于网络配置部分的说明，安装完后还是不行。第三次在安装时，由于在格式化分区时不小心把`mkfs.ext4 /dev/sda3`直接写出了`mkfs.ext4 /dev/sda1`，尼玛，我的Windows 8 就这样被毁了，于是还要重新安装Windows，而且安装Windows时，可能因为mkfs.ext4格式化的问题，安装Windows用的U盘在读取之后，却又无法安装，最后进PE才安装好Windows。于是再来安装Arch，于是就是几个反复的轮回。最后查到我的Realtek RTL8111/8169网卡也有别人遇到类似问题，于是有说Arch内置的r8169模块用不了，需要r8168模块，于是在Realtek官网下载到r8168，但内置的autorun.sh运行出错。最后发现AUR里有r8168模块，安装之，尼玛，还是不能上网。最后看到Network的wiki页面上说如果`dhcpcd`用不了，可以试试`dhclient`这个包，你知道，每次我都要在安装时的Live环境下才能联网，才能下载东西，所以真的是反复了很多轮回啊。终于`dhcpcd <interface>`可以了。

我的无线网卡时Broadcom的BCM4312，因为在`ip link`命令时，根本就看不到我的无线网卡的interface，然后又去查Arch的[Wireless Setup][wirless_setup]和[Broadcom wireless]的wiki（Arch的Wiki真的是详尽啊），尝试安装不同的驱动和模块，各种重启，最后发现还是自带的`b43`模块管用。我用的桌面环境时xfce4,然后又安装[`wicd`](https://wiki.archlinux.org/index.php/Wicd_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)) 和 `wicd-gtk`搞定网络管理。

终于可以happy的用了。

[arch]: https://www.archlinux.org/
[wirless_setup]: https://wiki.archlinux.org/index.php/Wireless_Setup_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)
[Broadcom wireless]: https://wiki.archlinux.org/index.php/Broadcom_wireless
[beginners_guide]: https://wiki.archlinux.org/index.php/Beginners'_Guide_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)
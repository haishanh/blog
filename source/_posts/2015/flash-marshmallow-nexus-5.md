title: 给Nexus 5吃棉花糖
date: 2015-10-06 21:51:54
tags:
---
昨天晚上刷了一下Nexus factory image的页面，还没有Marshmallow的下载地址。于是我[刷了个Flyme][weibo-post-flyme]玩了一下。睡觉前刷推，看到Marshmallow下载地址放出了。于是今天就刷了Marshmallow(其实，flyme还挺不错)，下面是一点记录。

<!-- more -->

### 下载factory image

先从[Factory Images for Nexus Devices][nexus-image]下载image。要记住下载完之后，一定要验证好md5sum和(或)sha1sum，尤其当你是从别的地方下载时。

### How to flash a system image

方法在前面提到官方的image下载页面就有详细的说明。

直接在Windows上刷的话可能会有USB driver上的问题。反正我是通过VirtualBox中的Ubuntu来刷机的，ubuntu中安装adb/fastboot tool也非常方便。如果是像我这样通过虚拟机刷机，不要忘记手机插上Host的USB后要把设备分配给Guest虚拟机。

我在刷的过程中，每次刷好bootloader和radio后，都会遇到下面的问题，试了几次都一样。

```
archive does not contain 'boot.sig'
archive does not contain 'recovery.sig'
failed to allocate 1043723460 bytes
error: update package missing system.img
```

解决方法就是把`image-hammerhead-mra58k.zip`解包后分开刷。

```
root@ubuvm:~/hammerhead-mra58k# unzip image-hammerhead-mra58k.zip 
Archive:  image-hammerhead-mra58k.zip
  inflating: android-info.txt        
  inflating: cache.img               
  inflating: boot.img                
  inflating: recovery.img            
  inflating: userdata.img            
  inflating: system.img 
```

其中刷`system.img`的过程时间会比较长点，要耐心一点。

```
root@ubuvm:~/hammerhead-mra58k# fastboot flash boot boot.img 
target reported max download size of 1073741824 bytes
sending 'boot' (9156 KB)...
OKAY [  2.108s]
writing 'boot'...
OKAY [  0.794s]
finished. total time: 2.902s
root@ubuvm:~/hammerhead-mra58k# fastboot flash recovery recovery.img 
target reported max download size of 1073741824 bytes
sending 'recovery' (10016 KB)...
OKAY [  2.083s]
writing 'recovery'...
OKAY [  0.851s]
finished. total time: 2.935s
root@ubuvm:~/hammerhead-mra58k# fastboot flash system system.img 
target reported max download size of 1073741824 bytes
erasing 'system'...
OKAY [  1.338s]
sending 'system' (1019261 KB)...
OKAY [215.548s]
writing 'system'...
OKAY [ 70.168s]
finished. total time: 287.059s
root@ubuvm:~/hammerhead-mra58k# fastboot flash cache cache.img 
target reported max download size of 1073741824 bytes
erasing 'cache'...
OKAY [  0.511s]
sending 'cache' (13348 KB)...
OKAY [  2.990s]
writing 'cache'...
OKAY [  1.495s]
finished. total time: 4.996s
root@ubuvm:~/hammerhead-mra58k# fastboot flash userdata userdata.img 
target reported max download size of 1073741824 bytes
erasing 'userdata'...
OKAY [ 12.978s]
sending 'userdata' (137318 KB)...
OKAY [ 29.485s]
writing 'userdata'...
OKAY [  9.136s]
finished. total time: 51.599s
```


最后一步falsh userdata，会抹除手机上的用户数据/应用等。上面的步骤完成后，重启手机。第一次启动的时间会比较长。

```
fastboot reboot
```

初始化设置的时候，会提示连接到WIFI，连接之后就会出现“正在检查网络连接...此过程最多可能需要2分钟”，不要信它，我等了十几分钟也没好，所以可以跳过，先不要连WIFI。



### root

参考xda上这个[回复][xda-root-post]。

目前Nexsus marshmallow要root的话，需要替换kernel。下载地址[在这][rootable-boot]，这个kernel相对原厂kernel的区别就是可root，没做其他优化和更改。

下载解压后，会有个boot.img，然后flash：

```
fastboot flash boot boot.img
```

再刷入第三方的recovery，目前最有名的就是TWRP的recovery了。Nexus 5的TWRP recovery[在这][nexus-5-twrp]。

```
fastboot flash recovery twrp-2.8.7.1-hammerhead.img
```

重启手机，进入recovery。然后卡刷Beta版的Supersu。下载地址[在这][supersu-beta]。到此就root完成了。


### Others

在5.0时，状态栏WIFI标志上会有感叹号，在6.0上问题还是一样。这也会导致系统更耗电。出现这个问题的原因，以及解决的方法，看这篇[文章][generate_204-reson]。



[weibo-post-flyme]: http://weibo.com/1351146375/CDPSxui4x?from=page_1005051351146375_profile&wvr=6&mod=weibotime&type=comment#_rnd1444136437374
[nexus-image]: https://developers.google.com/android/nexus/images
[xda-root-post]: http://forum.xda-developers.com/showpost.php?p=63147932&postcount=81
[rootable-boot]: http://www.mediafire.com/download/b69gq3wnanmmd4g/StephanMc_Kernel-hammerhead-20150904.zip
[nexus-5-twrp]: https://dl.twrp.me/hammerhead/
[supersu-beta]: https://download.chainfire.eu/740/SuperSU/BETA-SuperSU-v2.49.zip
[generate_204-reson]: http://www.noisyfox.cn/45.html

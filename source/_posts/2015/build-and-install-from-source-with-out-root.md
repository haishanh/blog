title: 无root权限从源码编译安装的痛苦
date: 2015-09-09 22:29:17
tags:
---

平时在公司上班的时候，我一般都是自己的办公电脑(Windows 7)上通过SSH软件登陆到公司的代码/编译服务器的。我比较喜欢用的是Xshell，但公司没有购买Xshell，公司前段时间发邮件明确指出不可以在公司使用这类软件，所以就想着用VNC。


平时偶尔也用VNC，但VNC中字体渲染看着实在难受。我们的服务器上有多个桌面环境，最近发现如果DE使用Gnome的话，安装[Ubuntu的字体][ubuntu-font]改改配置之后，字体渲染还可以接受。今天在使用的过程中，在VNC中用firefiox访问github时，github提示firefox版本过老。公司的编译服务器，我只有普通用户权限，所以没法升级firefox。平时我需要安装东西的时候，基本上都是从源码编译，安装到自己的目录。

<!--more-->

```bash
./configure --prefix=/home/haishanh/usr
make
make install
```



因为我知道浏览器这种东西，要想从源码编译出来会非常麻烦。所以我先是下载了Chrome的rpm包，安装时提示缺很多依赖。然后我就去下载[Firefox的Nightly Build][firefox-nightly]，运行之后，还是提示缺少GTK3。使用Nightly版，遇到一些服务器上现有软件/库文件过老的问题是很正常的，毕竟我们的服务器的系统是这样的老：

```bash
$ cat /etc/redhat-release 
Xxx Linux release 6.1 (Carbon)
$ uname -r
2.6.32-220.4.1.el6.x86_64
```

于是为了安装GTK3，我又干了如下的事：

```
$ history
  531  cd gtk+-3.6.4
  532  ls
  533  ./configure --prefix=/home/haishanh/usr64
  534  echo PKG_CONFIG_PATH
  535  echo $PKG_CONFIG_PATH
  536  export PKG_CONFIG_PATH=/home/haishanh/usr64/lib/pkgconfig
  537  ./configure --prefix=/home/haishanh/usr64
  538  cd ..
  539  wget http://ftp.gnome.org/pub/GNOME/sources/pango/1.37/pango-1.37.4.tar.xz
  540  tar xvf pango-1.37.4.tar.xz 
  541  cd pango-1.37.4
  542  ./configure --prefix=/home/haishanh/usr64
  543  cd ..
  544  cd gtk+-3.6.4
  545  ./configure --prefix=/home/haishanh/usr64
  546  cd ..
  547  wget http://cairographics.org/releases/cairo-1.14.2.tar.xz
  548  tar xvf cairo-1.14.2.tar.xz 
  549  cd cairo-1.14.2
  550  ls
  551  ./configure --prefix=/home/haishanh/usr64
  552  cd ..
  553  wget http://cairographics.org/releases/pixman-0.32.6.tar.gz
  554  tar xvf pixman-0.32.6.tar.gz 
  555  cd pixman-0.32.6
  556  ls
  557  ./configure --prefix=/home/haishanh/usr64
  558  make -j8
  559  make install
  560  cd ..
  561  cd cairo-1.14.2
  562  ./configure --prefix=/home/haishanh/usr64
  563  make -j8
  564  make install
  565  cd ..
  566  ls
  567  cd gtk+-3.6.4
  568  ./configure --prefix=/home/haishanh/usr64
  569  cd ../pango-1.37.4
  570  ./configure --prefix=/home/haishanh/usr64
  571  cd ../pixman-0.32.6
  572  ls
  573  ./configure --prefix=/home/haishanh/usr64
  574  make -j4
  575  make install
  576  cd ../cairo-1.14.2
  577  ./configure --prefix=/home/haishanh/usr64
  578  make -j8
  579  make install
  580  cd ../pango-1.37.4
  581  ./configure --prefix=/home/haishanh/usr64
  582  cd ..
  583  wget http://www.freedesktop.org/software/harfbuzz/release/harfbuzz-1.0.2.tar.bz2
  584  tar xvf harfbuzz-1.0.2.tar.bz2 
  585  cd harfbuzz-1.0.2
  586  ls
  587  ./configure --prefix=/home/haishanh/usr64
  588  make -j8
  589  make install
  590  cd ../pango-1.37.4
  591  ./configure --prefix=/home/haishanh/usr64
  592  cd ../
  593  wget http://www.freedesktop.org/software/fontconfig/release/fontconfig-2.11.92.tar.bz2
  594  tar xvf fontconfig-2.11.92.tar.bz2 
  595  cd fontconfig-2.11.92
  596  ls
  597  ./configure --prefix=/home/haishanh/usr64
  598  make -j8
  599  make install
  600  cd ../cairo-1.14.2
  601  ./configure --prefix=/home/haishanh/usr64 && make -j && make install
  602  cd ../pango-1.37.4
  603  ./configure --prefix=/home/haishanh/usr64 
  604  make -j8 && make install
  605  cd ../harfbuzz-1.0.2
  606  ./configure --prefix=/home/haishanh/usr64 && make -j8 && make install && echo "Done"
  607  cd ../pango-1.37.4
  608  ./configure --prefix=/home/haishanh/usr64 && make -j8 && make install && echo "Done"
  609  cd ../
  610  wget http://download.savannah.gnu.org/releases/freetype/freetype-2.6.tar.gz
  611  tar xvf freetype-2.6.tar.gz 
  612  cd freetype-2.6
  613  ./configure --prefix=/home/haishanh/usr64 && make -j8 && make install && echo "Done"
  614  cd ../harfbuzz-1.0.2
  615  ./configure --prefix=/home/haishanh/usr64 && make -j8 && make install && echo "Done"
  616  cd ../pango-1.37.4
  617  ./configure --prefix=/home/haishanh/usr64 && make -j8 && make install && echo "Done"
  618  cd ../gtk+-3.6.4
  619  ./configure --prefix=/home/haishanh/usr64 && make -j8 && make install && echo "Done"
  620  cd ..
  621  wget https://developer.gnome.org/atk/atk-html-2.16.0.tar.gz
  622  wget --no-check-certificate https://developer.gnome.org/atk/atk-html-2.16.0.tar.gz
  623  tar xvf atk-html-2.16.0.tar.gz 
  624  cd atk-html-2.16.0
  625  ./configure --prefix=/home/haishanh/usr64 && make -j8 && make install && echo "Done"
  626  ls
  627  cd ..
  628  rrm -rf atk-html-2.16.0
  629  rm -rf atk-html-2.16.0
  630  wget http://ftp.gnome.org/pub/gnome/sources/atk/2.16/atk-2.16.0.tar.xz
  631  tar xvf atk-2.16.0.tar.xz 
  632  cd atk-2.16.0
  633  ls
  634  ./configure --prefix=/home/haishanh/usr64 && make -j8 && make install && echo "Done"
  635  pkg-config --modversion glib-2.0
  636  echo $LD_LIBRARY_PATH
  637  export LD_LIBRARY_PATH=/home/haishanh/usr64/lib64:/home/haishanh/usr64/lib:${LD_LIBRARY_PATH}
  638  echo $LD_LIBRARY_PATH
  639  pkg-config --modversion glib-2.0
  640  ./configure --prefix=/home/haishanh/usr64 && make -j8 && make install && echo "Done"
  641  pwd
  642  cd ../gtk+-3.6.4
  643  ./configure --prefix=/home/haishanh/usr64 && make -j8 && make install && echo "Done"
  644  cd ..
  645  wget http://ftp.gnome.org/pub/GNOME/sources/gdk-pixbuf/2.30/gdk-pixbuf-2.30.8.tar.xz
  646  tar xvf gdk-pixbuf-2.30.8.tar.xz 
  647  cd gdk-pixbuf-2.30.8
  648  ./configure --prefix=/home/haishanh/usr64 && make -j8 && make install && echo "Done"
  649  cd ..
  650  wget ftp://ftp.remotesensing.org/pub/libtiff/tiff-4.0.4.tar.gz
  651  tar xvf tiff-4.0.4.tar.gz 
  652  cd tiff-4.0.4
  653  ./configure --prefix=/home/haishanh/usr64 && make -j8 && make install && echo "Done"
  654  cd ../gdk-pixbuf-2.30.8
  655  ./configure --prefix=/home/haishanh/usr64 && make -j8 && make install && echo "Done"
  656  ls ~/usr64/lib/libtiff.so
  657  dir=/home/haishanh/usr64
  658  ./configure --prefix=${dir} LDFLAG="-L${dir}/lib" CFLAGS="-I${dir}/include" CPPFLAGS="-I${dir}/include" CXXFLAGS="-I${dir}/include"
  659  ls ~/usr64/lib
  660  ls ~/usr64/lib/libtiff.so
  661  ./configure --prefix=${dir} LDFLAG='-L/home/haishanh/usr64/lib'
  662  ls ~/usr64/lib/libtif*
  663  ./configure --prefix=${dir} LDFLAG='-L/home/haishanh/usr64/lib' --without-libtiff
  664  make -j8 && make install
  665  cd ../gtk+-3.6.4
  666  ./configure --prefix=${dir} 
  667  cd ..
  668  wget http://ftp.gnome.org/pub/gnome/sources/at-spi2-atk/2.16/at-spi2-atk-2.16.0.tar.xz
  669  tar xvf at-spi2-atk-2.16.0.tar.xz 
  670  cd at-spi2-atk-2.16.0
  671  ./configure --prefix=/home/haishanh/usr64 && make -j8 && make install && echo "Done"
  672  history
```

这些命令，在history里面看起来只是一行一行的，但整个过程花费了我大量大量的effort。最要命是，我到最后还是没有编译安装好。

其实在没有root权限的情况，通过源码安装软件通常都是不可避免的(在此，安利一下Sublime Text 3，一个tar包下载下来，untar后直接就可以运行)。所以我平时也没少做。大部分时候，都能比较顺利的安装。但有些时候你要处理的是，依赖的安装，依赖的依赖的安装，依赖的依赖的依赖的安装，有的时候，我甚至忘了我是为了哪个库，去安装当前的库。

我觉得造成这样的原因主要是：

 1. 没有root权限，如果有root权限直接就可以通过包管理软件去安装，比如说EL上yum，Debian上apt-get，Arch上pacman
 2. 当前系统以及系统上的软件和库比较老
 3. Linux/Unix free的cost，这个地方free当然指的是自由。



[ubuntu-font]: http://font.ubuntu.com/
[firefox-nightly]: https://nightly.mozilla.org/



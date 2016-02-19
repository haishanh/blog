date: 2013-07-09 22:53:53
title: 如何在Cygwin中使用Z-shell
---
 
![zsh-in-cygwin](http://7fva40.com1.z0.glb.clouddn.com/zsh-in-cygwin.PNG)
 
[Cygwin](http://www.cygwin.com/)是个很牛逼的东西。Cygwin是Unix上的许多自由软件在Microsoft Windows上的实现。通过Cygwin，你就可以在Windows上使用你在Unix或Linux上熟悉的各种（不是全部）shell命令了。

<!--more-->   

你在安装Cygwin（就是那个setup.exe文件）时，在Select Packages步骤，可以通过搜索安装你想要装的软件包，比如说 你可以通过选择`Editers`下的`vim`来安装VIM。要注意不要选择错了就行，每个软件包Package栏会有该软件的名字和介绍，VIM的话是`vim: Vi IMproved - enhanced vi editor`。当你安装完发现你少装了某个包时，你完全可以再运行setup.exe，然后只选择你要安装的那个包就行了。    
Z-Shell是众多shell中的一个，补全功能比bash要强太多，同时命令和配置文件也都是兼容的。这个[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)是用来方便管理Z-Shell配置的，能让你更方便的使用插件和主题。要想在cygwin中用Z-Shell，其实就是在Select Packages时搜索`zsh`，选中`Shells`下的`zsh: The Z-Shell`。为了能方便的用oh-my-zsh,也要安装一下`Net`下的`curl`包。安装完后你运行`zsh`就应该可以进入Z-Shell环境了。退出Z-Shell，在Bash中运行以下命令： 

    curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh    

oh-my-zsh会自动安装好，在Linux下通过：

    chsh -s /bin/zsh       

可以让Z-Shell成为默认Shell，但cygwin下没有这条命令。但你可以更改/etc/passwd文件来达到更改用户shell的效果。     

    vim /etc/passwd

将你的用户名所在行中的`/bin/bash`改为`/bin/zsh`即可。oh-my-zsh中的内置主题列表可以参看[这个页面](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes)，我比较喜欢`ys`主题，直接将`~/.zshrc`中的`ZSH_THEME `值设置为`ys`即可。还是可以给cygwin terminal设置下背景色，字体等，terminal中间右击选择options设置相应参数即可。我在Windows上装了Monaco字体，选择10-point大小，背景色设置成`R=35, G=58, B=64`，透明度选择`Med.`。对了，我在用`ys`主题时，提示符中本应该显示我hostname的地方却显示不出来，但cygwin terminal的标题栏上却正常显示hostname信息，weird.    

--EOF--

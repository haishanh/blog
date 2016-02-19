date: 2013-03-18 10:34:17
title: 怎样在Ubuntu(Unity)上安装Sublime Text 2
---

本文是对Technoreply上Jevin写的[这篇文章](http://www.technoreply.com/how-to-install-sublime-text-2-on-ubuntu-12-04-unity/)的翻译和整理。      

Sublime Text是个碉堡了的文本编辑器。如果你还没有听过这个神器，快来[这里](http://www.sublimetext.com/)看看。          
我制作本指南是Linux版本的Sublime Text没有直接安装文件。这并不是什么大问题，但要是有个指南什么的当然更好。这篇文章正是教你如何在Unity中安装Sublime Text。

### 第一步
去Sublime Text官方网站下载对应Linux的tarfile文件。下面是解压缩`.tar.bz2`文件的命令：

	tar xf Sublime\ Text\ 2.0.1.tar.bz2    

如果你用的是64位系统，建议你下载针对64位的tarfile。

<!--more--> 

### 第二步
解压后你将得到一个名为`Sublime Text 2`的文件夹。该文件夹包含了所有Sublime Text需要的东西。其实此时在x界面直接进入这个文件夹双击`sublime_text`这个文件就可以运行Sublime Text了。但为了更优雅一点，请继续下面的步骤。      
将这个文件夹一刀一个更适合的位置，比如`/opt/`这个目录：

	sudo mv Sublime\ Text\ 2 /opt/

### 第三步

有时，你会想在终端中通过输入`sublime`来调用Sublime Text。我们可以通过在`/usr/bin`目录下建立一个软链接来实现：

	sudo ln -s /opt/Sublime\ Text\ 2/sublime_text /usr/bin/sublime

### 第四步

这时，我们需要在Unity里建立一个启动器。可以通过在`/usr/share/applications`目录下创建一个`.desktop`文件来实现：

	sudo sublime /usr/share/applications/sublime.desktop

这时Sublime Text就会启动，把一下内容复制进去，然后保存。

	
	[Desktop Entry]
	Version=1.0
	Name=Sublime Text 2
	# Only KDE 4 seems to use GenericName, so we reuse the KDE strings.
	# From Ubuntu's language-pack-kde-XX-base packages, version 9.04-20090413.
	GenericName=Text Editor
	 
	Exec=sublime
	Terminal=false
	Icon=/opt/Sublime Text 2/Icon/128x128/sublime_text.png
	Type=Application
	Categories=TextEditor;IDE;Development
	X-Ayatana-Desktop-Shortcuts=NewWindow
	 
	[NewWindow Shortcut Group]
	Name=New Window
	Exec=sublime -n
	TargetEnvironment=Unity

正如你看到的，这些参数都很直白明了，你可以自己试着改动里面的参数玩玩。

### 第五步

如果你想用Sublime Text打开全部的文本文件，最简单的方法就是更改原本的默认打开程序列表：

	sudo sublime /usr/share/applications/defaults.list

用sublime.desktop取代所有gedit.desktop出现的地方。
搞定！你现在就像职业程序员一样在Ubuntu上拥有了神器Sublime Text 2
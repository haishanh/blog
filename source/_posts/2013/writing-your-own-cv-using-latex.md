date: 2013-09-11 18:41:09
title: 用LaTex来写简历
---

![cv_xiaolong](http://7fva40.com1.z0.glb.clouddn.com/201309/cv_xiaolong.PNG)

嗯，又到了找工作的季节了。
用M$ Word写出的简历是不是没那么酷[^1]，那或许你可以试试用[LaTex][]来写简历，开源，免费，效果棒。
[moderncv]类是一个超棒的LaTex简历模板，用moderncv作出的简历也非常的漂亮，看看图就知道了。

接下来说说怎么做，你也可以参考[这篇](http://blog.sina.com.cn/s/blog_53da0ca70101bwd1.html)文章。
其实之前很早在网上看到moderncv做的简历时，就被吸引了，然后自己尝试了做，完全不懂的情况下，走了不上弯路。

<!--more--> 

首先你得有个LaTex环境，不同的系统各不相同，比如我用的是Arch Linux，那么就可以通过安装Tex Live来获得LaTex环境。

    sudo pacman -S texlive-most

上面的命令获得Tex Live应用

    sudo pacman -S texlive-lang

texlive-lang 提供个性化的设置和非英语特性，所以也要安装。具体参见[这里](https://wiki.archlinux.org/index.php/TeXLive_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))。

接下来就是下载moderncv包，可以到[这里](http://www.ctan.org/tex-archive/macros/latex/contrib/moderncv)下载。包中的examples文件夹中也有几个用moderncv生成的pdf的例子，可以看一看。包中有个**template-zh.tex**是中文cv的模板，但我想说的是**不要用这个模板**，虽然可以用起来可能没问题，但如果想修改中文字体的话，会很麻烦。我之前就是用这个模板，耽误了很多时间。包中的**template.tex**才是我们需要的模板文件。
我们先来玩一玩，用你最喜欢的编辑器打开这个模板，文件的下面部分是简历信息，你可以试着改改，然后运行

    xelatex template.tex
    
如果一些正常的话，同一目录下就会生成一个叫template.pdf的pdf文件，打开，你应该能看到你的改动，Oh yeah。

接下来是样式的自定义（以"%"开始的语句在LaTex中是注释）

```
    %设置文档字体大小，纸张大小，字体族
    \documentclass[10pt,a4paper,sans]{moderncv}
    % 设置主题
    \moderncvstyle{classic}
    % 设置主题颜色
    \moderncvcolor{blue}
    %\renewcommand{\familydefault}{\sfdefault}
    %\nopagenumbers{}
    % 设置编码方式
    %\usepackage[utf8]{inputenc}
    %\usepackage{CJKutf8}
    % 调整页面边距
    \usepackage[scale=0.75]{geometry}
    %\setlength{\hintscolumnwidth}{3cm}
    %\setlength{\makecvtitlenamewidth}{10cm}
```
以上每一条语句在**template.tex**中都有解释，也列出了可用参数，比如主题就有 classic, casual, oldstyle, banking 可以选择。中文的解释在**template-zh.tex**中也能对应的上，可以参考一下(只是不要用这个文件作模板)。

下面的设置对中文简历很重要。
```
    %中文字体设置
    \usepackage{fontspec}
    \usepackage{xunicode}
    \usepackage{xeCJK}
    %主字体
    \setmainfont{Minion Pro}
    %无衬线字体
    \setsansfont{Myriad Pro}
    %等宽字体
    \setmonofont{Meslo LG M}
    %主要中文字体
    \setCJKmainfont{SimSun}
    %无衬线中文字体
    \setCJKsansfont{KaiTi}
    %等宽中文字体
    \setCJKmonofont{SimHei}
    %\setCJKmathfont{}
```
现在你就可以随便写中文简历了。你也可以自定义中文字体，比如我，直接把所有中文字体都改成了 *文泉驿正黑*。
```
    %中文字体
    \setCJKmainfont{文泉驿正黑}
    \setCJKsansfont{文泉驿正黑}
    \setCJKmonofont{文泉驿正黑}
```

个人信息的填写也都有解释，并不一定要按它的格式。

--
话说，昨晚iPhone 5C和iPhone 5S都发布了，原来iPhone 5C根本不是廉价版，是为了替代iPhone 5。而且大陆和香港的差价好大啊。
    
    // 2013/09/11 22:00 exchange rate
    //iPhone 5C 16GB    
    Price_diff = Price_cn - Price_hk = RMB4488 - HK$4688 ≈ RMB4488 - RMB3698.90741 = RMB789.09259
    //iPhone 5S 64GB    
    Price_diff = Price_cn - Price_hk = RMB6888 - HK$7188 ≈ RMB6888 - RMB5671.44762 = RMB1216.55238
    

擦~



[LaTex]:http://www.latex-project.org/
[moderncv]: http://www.ctan.org/pkg/moderncv
[^1]: 这里绝不是说用MS Word就写不出酷酷的简历 =.=


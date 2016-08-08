---
title: Pug is so cool
date: 2016-07-28
---

因为之前用 Python, 其最有名的 template 就是 [Jinja2](http://jinja.pocoo.org/)，所以在开始使用 JavaScript 的 html template 的时候，我就尝试和 Jinja2 非常相似的 [Swig](http://paularmstrong.github.io/swig/)。后来觉得 Swig 写起来不够自由，所以后面又使用 [ejs](https://github.com/mde/ejs)，ejs 写起来是很自由，但写完之后，读起来很难受，改起来也很难受。

而目前我最喜欢的 template engine 是 Pug（之前叫Jade）.

Pug 用起来很简单。层级分明，读起来也很舒服。

```
doctype html
html
  head
    meta(charset="utf-8")
    title Hello
  body
    main
      .left
        p Just some random text
      .right
        p Again, some random text
    footer
      p Made by Haishan
```

第一眼看起来你会觉得有点像是用[`emmet`](http://emmet.io/)写 html。以致于我第一次看到 Pug(那个时候叫Jade)的时候就觉得这玩意是用来写 html，但当 template 用肯定不怎么样。但其实 Pug 本来就是用作 template 的。


如果只是字面值，可以这样：

```
p Hello world!
```

作为 template 我们也可以使用 Javascript 变量，比如下面的例子中，`pageName` 就是一个变量，这个变量可以是 render 的时候传进去的，也可以是该 Pug 文件前面已经对其进行了定义。

```
title= pageName
```

也可以使用 `!=` 符号，这样后面变量中的内容不会被转义。而使用 `=`，变量的内容会做 html tag 转义。

```
p!= pageParagraph1
```

当然也可以是这样：

```
p= 'This code is <escaped>!'
p!= 'This code is <strong>not</strong> escaped!'
```

你可以考虑一下，下面的内容会渲染成什么样

```
p!= 'This code is <strong>not</strong> escaped!'
p= 'This code is <strong>not</strong> escaped!'
p 'This code is <strong>not</strong> escaped!'
p This code is <strong>not</strong> escaped!
```

想知道答案？不告诉你，自己试试好，现在就试。

我们当然可以直接在 Pug 中去 import 其他 Pug 文件，使用的是 `include` 命令，比如下面的例子中会把 `cnt/hello.pug` 的内容包含进来：

```
main.content
  include cnt/hello
```

如果是 Pug 文件的话，后缀可省。比如是要包含 html 文件的可以 `include cnt/another.html`。所以我有时想 inline svg 的时候，我就 `include logo.svg`


tag 中[属性在 pug 中这样写](http://jade-lang.com/reference/attributes/)：

```
a(href="https://github.com/haishanh", alt="Haishan on GitHub") Haishan on GitHub
```

render 之后就是这样：

```html
<a href="https://github.com/haishanh" alt="Haishan on GitHub">Haishan on GitHub</a>
```

为了易读可以写成这样：

```
a(
  href="https://github.com/haishanh",
  alt="Haishan on GitHub"
) Haishan on GitHub
```

作为 template，我们也可以在 pug 中[直接使用 Javascript 代码](http://jade-lang.com/reference/code/)。

```
.container
  - var i = 0;
  - while(i++ < 3) {
      li same item
  - }
```

上面的代码 render 后的结果是这样的：

```html
<div class="container">
  <li>same item</li>
  <li>same item</li>
  <li>same item</li>
</div>
```

如果层级分明的时候，可以不写大括号，写成这样：

```
.container
  - var i = 0;
  - while(i++ < 3)
    li same item
```

再提一句要注意层级：

```
.container
  - var i = 0;
  - while(i < 3)
    li same item
    - i++;
```

假设最后一句的 `- i++` 向前缩进2格的话，就会死循环了。

当然 Pug 还有很多有用的 feature，比如 [extends][], [mixins][]，等等。具体的自己再多看文档咯。


[extends]: http://jade-lang.com/reference/extends/
[mixins]: http://jade-lang.com/reference/mixins/

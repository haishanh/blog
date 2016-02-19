title: GCC code coverage小例子
date: 2015-11-21 21:29:40
tags:
---
有同事问我code coverage的问题，但其实这方面我毛都不懂。所以我也去学习了一下。

Code coverage其实就是去检查我们的测试case是不是把我们的代码都覆盖到了。如果要进行code coverage，release版本是binary不行的，必须要用编译出带有coverage instrumentation的binary。要进行code coverage，大概的步骤是：

 1. 编译出可以用于coverage的binary
 2. 跑test case
 3. 收集coverage信息

我自己写了个小例子，源码可以在[这里][example-repo]。我们可以按照这个例子，去执行上面的步骤。首先你的机器上需要安装有[`make`][make], [`gcc`][gcc]以及[`lcov`][lcov]，gcov是gcc中用于测试code coverage的工具，而lcov是对gcov的一个扩展。

<!-- more -->

因为在有些项目中，我们除了编译出可执行文件外，还可能需要编译出一些库文件供别人使用。下载例子之后，我们首先进入**cov**目录。其中`hello.c`包含函数`say_hello()`，编译成`libhello.so`。`bye.c`包含函数`say_bye()`，编译成`libbye.so`。`main.c`中会根据不同的输入参数调用`say_hello()`或`say_bye()`。最终编译出可执行文件`say`。

### 编译

运行`make`就可以编译出这些库文件和可执行文件。

```sh
$ ls  
bye.c  hello.c  main.c  Makefile  say.h

$ make
......

$ ls
bye.c  bye.o  hello.c  hello.o  libbye.so  libhello.so  main.c  Makefile  say  say.h

$ ls -lh say lib*.so
-rwxr-xr-x 1 vagrant vagrant 6.2K Nov 22 01:24 libbye.so
-rwxr-xr-x 1 vagrant vagrant 6.2K Nov 22 01:24 libhello.so
-rwxr-xr-x 1 vagrant vagrant 7.3K Nov 22 01:24 say
```

如果我们运行`make coverage`可以编译出可用于coverage测试的库和可执行文件。

```
$ make coverage
......

$ ls
bye.c     bye.o    hello.gcno  libbye.so    main.c     Makefile  say.h
bye.gcno  hello.c  hello.o     libhello.so  main.gcno  say

$ ls -lh say lib*.so
-rwxr-xr-x 1 vagrant vagrant 22K Nov 22 01:25 libbye.so
-rwxr-xr-x 1 vagrant vagrant 22K Nov 22 01:25 libhello.so
-rwxr-xr-x 1 vagrant vagrant 23K Nov 22 01:25 say
```

首先我们可以看到，相比没有使用coverage选项，多出了几个`.gcno`文件，库和可执行文件的大小也比之前大出许多。

这是因为我们在`make coverage`时，gcc的编译和链接过程都加入了`-coverage`选项。对于gcc，在编译阶段使用`-coverage`选项相当于使用`-fprofile-arcs -ftest-coverage`，在链接时使用`-coverage`选项相当于使用了`-lgov`。

### 测试

之后我们便可以进行测试

```
export LD_LIBRARY_PATH=${PWD}:${LD_LIBRARY_PATH}
./say h
```

其中第一步，是为了让`say`在运行时可以找到`libhello.so`和`libbye.so`这两个库。第二步就是我们的测试步骤，其中`h`参数，可以让我们只调用`say_hello()`。

此时，我们会发现我们的目录里，又多出了几个`.gcda`文件。这些文件就是生成的coverage信息文件。

```
$ ls
bye.c     bye.gcno  hello.c     hello.gcno  libbye.so    main.c     main.gcno  say
bye.gcda  bye.o     hello.gcda  hello.o     libhello.so  main.gcda  Makefile   say.h
```

### 收集coverage信息

```
$ lcov -c -d ./ -o coverage.info
Capturing coverage data from ./
Found gcov version: 5.2.0
Scanning ./ for .gcda files ...
Found 3 data files in ./
Processing main.gcda
Processing bye.gcda
Processing hello.gcda
Finished .info-file creation

$ lcov --remove coverage.info "/usr*" -o coverage.info
Reading tracefile coverage.info
Deleted 0 files
Writing data to coverage.info
Summary coverage rate:
  lines......: 53.3% (8 of 15 lines)
  functions..: 66.7% (2 of 3 functions)
  branches...: no data found

$ genhtml coverage.info -o coverage
Reading data file coverage.info
Found 3 entries.
Found common filename prefix "/home/vagrant/coverage-exapmle/coverage"
Writing .css and .png files.
Generating output.
Processing file cov/bye.c
Processing file cov/main.c
Processing file cov/hello.c
Writing directory view page.
Overall coverage rate:
  lines......: 53.3% (8 of 15 lines)
  functions..: 66.7% (2 of 3 functions)
```

第一步，使用`lcov`生成`coverage.ino`文件。第二步，我们是去除掉系统的源码调用记录，比如`stdio.h`什么的。第三步是在coverage目录中生成html格式的报告。此时我们进入coverage，运行`python -m SimpleHTTPServer`就可以通过浏览器访问本机的8000端口来查看coverage报告了。

从上面的输出，我们可以看到，我们的测试覆盖到了源代码中53.3%行，和66.7%的函数。假设我们把测试步骤中的`./say h`改成`./say`，会发现函数的覆盖率变成了100%。

### 自动化

在生产环境中，我们通常可以把上面的两个步骤自动化。我们可以把测试的运行写成脚本，然后在Makefile中调用该脚本，再收集coverage信息。进入**auto**目录，运行`make coverage`你就能明白了。



Ref: [Code coverage for C using gcc and lcov][cov-ref]

[example-repo]: https://github.com/haishanh/gcc-code-coverage-example
[make]: https://www.gnu.org/software/make/
[gcc]: https://gcc.gnu.org/
[lcov]: http://ltp.sourceforge.net/coverage/lcov.php
[cov-ref]: http://ernstsson.net/post/23930158749/code-coverage-for-c-using-gcc-and-lcov


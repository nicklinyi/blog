---
title: Debug Madagascar程序
date: 2016-06-23 16:45:30
updated: 2016-06-23 12:38:00
author: Nick
categories: [地震学软件]
tags: [Madagascar, gdb]
---

## 前言
当我们在 Madagascar API框架上写完自己的代码后，对于简单的程序，可以采用在源文件中增加`printf`或者`write`语句等打印一些变量信息，但比较麻烦的是每次增加一些`printf`等语句后，需要重新编译源代码，造成不必要的麻烦。因此，这里简单的介绍如何使用`gdb`来debug Madagascar程序。

## 安装gdb

首先，需要查看自己的计算机是否安装了`gdb`软件，我们采用`which`命令。

    $ which gdb

如果终端中返回`gdb`可执行二进制文件的目录则表示已安装`gdb`，若提示找不到该命令则需要安装`gdb`。安装比较简单，输入下面语句即可安装`gdb`

    $ sudo apt-get install gdb

<!-- more -->

## 使用

安装完`gdb`后，我们首先进入自己编写的Madagascar程序的源文件目录，编译该代码生成可执行二进制文件。
假设我们的这个代码是实现对两个`.rsf`文件进行相加，最终得到二者之和。生成的可执行二进制文件名为`sfadd`,那么我们可以通过如下方式对其debug.

首先在终端输入`$ gdb ./sfadd`进入debug模式。在终端中表现为如下结果：

    (gdb)

由于该代码需要接收两个`.rsf`文件作为其`args`。这里假设两个文件分别为`add1.rsf`,`add2.rsf`,输出的结果文件为`add.rsf`.那么接着在gdb中，继续键入参数信息：

    (gdb) set args "file1=add1.rsf" "file2=add2.rsf" "out=add.rsf"

这个命令通过`set args`来传递`args`信息，之后我们可以通过`show args`来确认参数信息。

    (gdb) show args
    Argument list to give program being debugged when it is started is "file1=add1.rsf" "file2=add2.rsf" "out=add.rsf"
    (gdb)

说明:
1.所有的参数文件必须与二进制可执行文件在同一文件夹下，如果不在，则需要显式给出其路径
2.`args`中的`file1`,`file2`是对应自己Madagascar程序中参数文件名，根据自己的设置修改

最后，我们可以在`gdb`中输入`run`来跑自己的程序，还有一些其他的操作，比如设置断点以及打印变量等与正常调用`gdb`一致，这里不再赘述。大家可以参考在线gnu的gdb文档，里边有非常详细的介绍。



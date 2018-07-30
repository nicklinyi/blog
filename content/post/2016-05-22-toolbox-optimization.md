---
title: Linux 下安装 TOOLBOX-OPTIMIZATION
date: 2016-05-22
categories: ['安装']
tags: [toolbox]
author: Lloyd
slug: toolbox-optimization
---

## 下载

下载链接: <https://seiscope2.obs.ujf-grenoble.fr/SEISCOPE-OPTIMIZATION-TOOLBOX>

## 编译

将下载的软件解压后，进入`doc/`目录下，查看manual文档进行编译。

## 修改Makefile.inc

`Makefile.inc`文件位于根目录下，只需要修改与自己系统相适的编译器即可，一般情况不用作任何改动，因为其默认使用的是intel的编译器。

### Compilation

在根目录下，运行
``` {.console}
$ make lib
```
即可在目录`lib/`下生成`libSEISCOPE_OPTIM.a`,该文件对于后面安装TOY2DAC十分有用。



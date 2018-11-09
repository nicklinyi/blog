---
title: Madagascar下使用 LaTeX
date: 2016-05-24
tags: ["Madagascar", "LaTeX", "Paper"]
categories: [地震学软件]
---

之前一直尝试在 Madagascar 下使用 latex 功能，但一直没有实现。也没有太多地时间来研究这个。今天，实在是忍不住了，开始重新研究起来。首先，`cd`到`book`目录下，任选一个文件夹进去，然后运行`$ scons`，发现总是出现权限不够错误。后来再进入到其他文件夹，发现还是存在同样的问题。但这次发现问题的根源所在了。

Madagascar 下使用 scons 来编译 latex 将会在`$RSFROOT/share/madagascar`下创建一个本地文件夹的副本，其目的是为了用来之后`lock`图片所用，而由于不管将 Madagascar 安装在`/opt`下，还是`$HOME`下的某个目录，`$RSFROOT`都默认不是 766 权限。因此其下的`share/madagascar`也一样。通过测试发现，只有`share/madagascar`为 766 权限时，madagascar 才能向里边写文件.因此第一步就是将该路径设置为 766 .

按上述设置后，scons 暂时不报错了，也可以正常编译 latex 文件，但是由于 madagascar 下的 tex 基本是与地球物理相关的，因此 latex 下的 texmf 缺少一些 cls 文件，可从 sourceforge 上下载 segtex ，然后将源码解压并放在`$HOME/texmf`下，然后通过`texhash`来将其添加到现有的 latex 的 texmf 中。

<!-- more -->

虽然，按上述操作后 latex 也不报错了，但 latex 编译完后，scons 还是会报一些小的错误，主要是关于阅读和打印 pdf 的问题，因为采用 tex.py 中的 Paper 来编译 latex 文件时，编译完会自动打开并打印文件，而在其打印机下并未写打印机名称以及地址，因此总会报错，毕竟这是一个 machine-depending 的问题， Madagascar 没有处理好这个问题是很正常的。这算是 Madagascar 的一个小 bug 。但并不影响读者正常编译和使用。

但笔者还是要解决这个问题，因为并不是每个人编译完文件都想马上打开 pdf 文件，也没有这个必要这么做，因此，我们找到`$RSFROOT`中的 tex.py 文件，注释掉其中的两行代码，就可解决上述问题。

至于 latex 编译时会遇到系统中缺少一些字体的情况，并不是本文讨论的重点。大家可自行谷歌予以解决。

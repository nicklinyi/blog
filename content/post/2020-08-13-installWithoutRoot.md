---
title: 在没有 root 权限的情况下安装 Madagascar
date: 2020-08-13
lastmod: 2020-08-13
tags: [Madagascar]
categories: ['安装']
slug: madagascar_noRoot
---

在有 root 的权限下安装 Madagascar 比较容易，请参考 <https://blog.nickwhyy.top/madagascar/>。
本文主要探讨在没有 root 权限的场合下（比如服务器上）安装 Madagascar。

本人所用的服务器是 CentOS 7.5 系统，正常安装依赖包直接用 yum 安装即可，但在没有 root 的权限下
无法直接安装各种依赖包，一般有两种方式来安装软件：
- 1.去网上找一些 rpm 包，然后将其解压，并添加路径到`~/.bashrc`；
- 2.找相关安装包的源代码，然后手动编译。

一般而言，安装不带 `sfpen` 的 Madagascar 十分容易，直接在 `src`目录下运行 `./configure`然后
再`make`, `make install`即可安装成功。但`sfpen`程序依赖于 `libXaw-devel` 图形库，因此，必须
先安装该软件包，才能安装一个比较完整的 Madagascar。

`libX*`相关的源代码可在<ftp://mirror.csclub.uwaterloo.ca/x.org/X11R7.7-RC1/src/lib/>上进行
下载。下面以安装`libXaw`为例，简单进行说明

### 编译，安装`libXaw`

``` {.console}
$ tar -xvf libXaw-1.0.10.tar.gz
$ cd libXaw-1.0.10
$ ./configure --prefix=/home/lloyd/.local
$ make
$ make install
```

>
这里大概率会提示缺少`xt`等，需要上<ftp://mirror.csclub.uwaterloo.ca/x.org/X11R7.7-RC1/src/lib/>
下载`libXt*.tar.gz`,然后按照上面的步骤安装`libXt`，可能会提示缺少`ICE`等，继续按照上面的操作流程，
补完对应缺少的依赖包，每一个依赖包都按照解压, `configure`， `make`， `make install`四部曲进行。

>
可能遇到了问题是，比如`ICE`依赖`xtrans`，我装完了`xtrans`,回过来再装`ICE`,结果提示系统里找不到`xtrans`，
然后可能想到，把xtrans的安装路径下的`lib`添加到`LD_LIBRARY_PATH`中，结果发现还是没用。主要原因在于
`configure`调用了系统的pkg_config程序，而系统的pkg_config程序不会搜索非标准目录(如`/usr/lib`)下安装的程序,
因此，需要将`xtrans`的pkgconfig路径添加到系统的`PKG_CONFIG_PATH`中，具体操作如下,在`~/.bashrc`中添加：

```
export PKG_CONFIG_PATH=${PKG_CONFIG_PATH}:${HOME}/.local/share/pkgconfig
export PKG_CONFIG_PATH=${PKG_CONFIG_PATH}:${HOME}/.local/lib/pkgconfig
```

重复刚才的所有操作，应该就能成功地安装libXaw了，然后开始安装 Madagascar。

### 编译，安装 Madagascar
进入Madagascar的源文件根目录，输入`configure API=c++,f90,matlab,python --prefix=/home/lloyd/.local/rsf`
,结果发现configure的结果中`X11`是 no，但是刚才的那步确实是安装了libXaw，这时需要修改`framework/configure.py`,
找到里面的xinc, xlib， 将自己刚才安装libXaw的路径补充进去，然后重新configure, 即可解决刚才的问题，从而安装
一个比较完整的Madagascar，剩下的安装步骤见<https://blog.nickwhyy.top/madagascar/>。

### libXaw安装方式2
上这个https://centos.pkgs.org/7/centos-x86_64/libXaw-devel-1.0.13-4.el7.x86_64.rpm.html下载libXaw-devel*.rpm
包，然后查看该软件包所依赖的其它软件包(即网页中的Requires一栏)，逐个下载依赖包，然后check每个依赖包对应需要下载
的其它依赖软件，将所有的依赖软件都下载下来。且放在同一个文件夹下，运行下面的命令，并将`libXaw-devel-1.0.13-4.el7.x86_64.rpm
`换成刚才下载的其它软件包的名称，依次解压软件包到usr文件夹下。然后你会发现usr目录下会有`include`，`lib`等目录，
将`include`和`lib`分别添加到Madagascar的$RSFSRC/framework/configure.py中的xinc 和 xlib中，然后就可以安装
完整的Madagascar了。

```
$ rpm2cpio libXaw-devel-1.0.13-4.el7.x86_64.rpm | cpio -idvm
```

>
注意，方法2仅仅是理论上可行，本人未进行测试。如果测试有问题，可转向方法1，进行源码编译，一般不会有太大的问题。


### 修订历史

- 2020-08-13: 初稿；

---
title: Linux 下安装 Seismic Unix
date: 2016-05-12
tags: ['SU']
categories: ['安装']
slug: seismic-unix
---

Seismic Unix是Colorado School of Mines开发的一款地震数据处理软件。最新的版本代号为44R1。
这里选择43R8版本进行安装。

下载地址：<http://www.cwp.mines.edu/cwpcodes/index.html>

## 安装依赖包

``` {.console}
$ sudo apt-get install build-essential
$ sudo apt-get install libx11-dev
$ sudo apt-get install libxt-dev
$ sudo apt-get install freeglut3-dev  
$ sudo apt-get install libxmu-dev
$ sudo apt-get install libxi-dev
$ sudo apt-get install gfortran
```

## 配置环境变量

一般软件的安装都是先编译，之后配置环境变量，su不同，正好相反，需要先配置环境变量。

向`~/.bashrc`中加入如下语句以配置环境变量。

``` {.bash}
# Seismic Unix 44R1
export CWPROOT=$HOME/opt/cwp
export PATH=$PATH:$CWPROOT/bin
```

注：`CWPROOT`为su的安装路径，请根据自己想要安装的路径确定，这里是放在了`$HOME/opt/cwp`。不要以root权限安装su，若造成系统不稳定，后果自负!!!

## 解压
```
$ cd ~
$ mkdir opt
$ cd opt
$ mkdir cwp
$ cd cwp
$ wget ftp://ftp.cwp.mines.edu/pub/cwpcodes/cwp_su_all_44R1.tgz
$ tar -xvf cwp_su_all_44R1.tgz
```

## 编译，安装
编译前，可以选择修改一下`src/Makefile.config`文件。
```{.makefile}
LINEHDRFLAG =
#XDRFLAG =  -DSUXDR  建议将此行注释掉，此行的目的在于将所有的su文件都变成big-endian的格式
ENDIANFLAG = -DCWP_LITTLE_ENDIAN
LARGE_FILE_FLAG = -D_FILE_OFFSET_BITS=64 -D_LARGEFILE64_SOURCE
```
``` {.console}
$ cd src
$ make install
$ make xtinstall
$ make finstall
$ make mglinstall
```



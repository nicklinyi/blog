---
title: Madagascar 的安装
date: 2015-05-14
lastmod: 2021-08-31
tags: [Madagascar]
categories: ['安装']
slug: madagascar
---



与Seismic Unix类似，Madagascar也是一款地震数据处理软件，其开发者众多，内置的软件也非常多，
缺点就是代码注释比较少，不容易懂。给的示例虽多，但也不容易懂，对于初学者而言十分困难。

下载最新的Release版本：<http://github.com/ahay/src.git>


## Linux 下安装Madagascar

### 安装依赖包

``` {.console}
# 必须安装的依赖包
$ sudo apt install libxaw7-dev
# 建议安装的依赖包
$ sudo apt install freeglut3-dev
$ sudo apt install libnetpbm10-dev
$ sudo apt install libgd-dev  
$ sudo apt install libplplot-dev
$ sudo apt install libavcodec-dev
$ sudo apt install libcairo2-dev
$ sudo apt install libjpeg-dev
$ sudo apt install swig
$ sudo apt install python-dev
$ sudo apt install python-numpy
$ sudo apt install g++
$ sudo apt install gfortran
$ sudo apt install libopenmpi-dev
$ sudo apt install libfftw3-dev
$ sudo apt install libsuitesparse-dev
# 不建议安装的包
$ sudo apt install python-epydoc
# 安装 octave API 需要下面的包
$ sudo apt install octave liboctave-dev
```

### 编译，安装

``` {.console}
$ git clone https://github.com/ahay/src.git madagascar-3.2
$ cd madagascar-3.2
$ ./configure API=c++,f90,python,matlab,octave --prefix=/home/lloyd/rsf3.2 # 额外安装C++，Fortran, Python, Matlab, Octave 五个API
$ make
$ make install
```

>
若出现`checking for mkoctfile ... (cached) no`的错误，主要原因是缺`liboctave-dev`,重新安装`liboctave-dev`即可。

### 配置环境变量

向`~/.bashrc`中加入如下语句以配置环境变量。

``` {.bash}
# Madagascar 3.2
export RSFROOT=/home/lloyd/rsf3.2
source $RSFROOT/share/madagascar/etc/env.sh
```

## macOS High Sierra 下安装Madagascar
### 安装依赖包
- 安装[MacPorts](https://www.macports.org/),进官网，下载安装文件打开安装即可。
- 安装Xcode，从App Store里安装即可
- X11，可通过安装[Xquartz](https://www.xquartz.org/)来获得X11，进官网下载安装文件安装即可
```
## FFTW
$ sudo port install fftw-3-single
$ sudo port install fftw-3 +gfortran
## MPI
sudo port install openmpi
sudo port select --set mpi openmpi-mp-fortran
## ppmpen
sudo port install libnetpbm
```

### 编译，安装
macOS 安装 Matlab API 限制比较多，首先需要在`~/.bash_profile`中添加 Matlab 的安装路径。

```bash
# MATLAB 20XXX, XXX 为版本号，比如 Matlab 2018a etc.
export PATH=$PATH:/Applications/MATLAB_R20XXX.app/bin
```

``` {.console}
## 在Madagascar 源码的根目录下执行下列操作
$ ./configure CC=clang CXX=clang++ API=python,f90,matlab,octave --prefix=/Users/lloyd/rsf # 额外安装C++,Python,Fortran,Matlab,Octave五个API
$ make
$ make install
```
>
另外，需要考虑 Matlab mex 支持的编译器，比如最新的 macOS High Sierra 系统中 Xcode 的版本为9.X，而 Matlab 2016x 等不支持该版本的编译器。关于 Matlab 版本与对应的 Xcode 版本对应关系详见：
https://www.mathworks.com/support/sysreq/previous_releases.html

### 配置环境变量

向`~/.bash_profile`中加入如下语句以配置环境变量。

``` {.bash}
# Madagascar 3.2
export RSFROOT=/Users/lloyd/rsf
source $RSFROOT/share/madagascar/etc/env.sh
```

### 参考

- https://www.mathworks.com/support/sysreq/previous_releases.html
- https://www.mathworks.com/matlabcentral/answers/361388-matlab-2016b-on-mac-with-xcode-9-error

### 修订历史

- 2016-05-22: 初稿；
- 2018-04-28: 更新 macOS High Sierra 下安装 Madagascar;
- 2018-05-20: 更新 Linux 安装 Matlab API;
- 2018-05-22: 更新 macOS High Sierra 下安装 Matlab API;
- 2018-08-12：新增 Octave API
- 2021-08-31: 更新至 madagascar3.2;


---
title: Linux 下安装 AxiSEM3D
date: 2022-01-17

tags: [AxiSEM3D]
categories: ['安装']
slug: axisem3dinstall
---

## 前言

AxiSEM3D有两个版本。之前介绍过安装老的版本见https://blog.nickwhyy.top/post/axisem3d/ 。今天介绍安装新版本的方法。

## 下载

要下载AxiSEM3D，可以在终端中输入以下命令：

```bash
# 新版本为AxiSEM-3D
$ git clone https://github.com/kuangdai/AxiSEM-3D.git AxiSEM3D
# synchronize to github
$ git -C AxiSEM3D pull
```

## 安装依赖包

- [METIS](https://blog.nickwhyy.top/post/metis/) 安装版本大于5.1， 只需安装32位的版本即可
- Eigen 需要其版本号大于或等于3.3.9，由于该版本目前一直在更新，可能会导致编译的时候出问题。建议使用AxiSEM3D的开发者提供的版本**https://github.com/kuangdai/AxiSEM-3D/files/5476700/eigen-master.zip**
- Boost 需要其版本号大于或等于1.73.0，下载链接为https://boostorg.jfrog.io/artifactory/main/release/1.73.0/source/boost_1_73_0.tar.bz2

- NetCDF, FFTW服务器上安装了，直接用即可

## 安装

Eigen, 和 Boost都是直接下载，然后解压即可。

```bash
# 在AxiSEM的目录下，新建一个build文件夹，然后进入该文件夹
$ mkdir -p build && cd $_
$ rm -rf ./* && cmake  -Dcxx=mpiicpc -Deigen=$HOME/.axisem3d_depends/eigen3 -Dboost=$HOME/.axisem3d_depends/boost_1_73_0  -Dmetis=$HOME/.axisem3d_depends/metis-5.1.0 -Dnetcdf=/home/opt/netcdf/c-4.7.4-intel -Dfftw=/home/opt/fftw/3.3.8-intel -Dflags="-O3 -std=c++17" ../SOLVER/
# 使用高版本的gcc编译器（>=5）
$ scl enable devtoolset 11 bash
# 编译
$ make -j8
```

## 测试
1. 复制一个`sbatch`脚本

```bash
$ cp /home/opt/intel.sh .
```
2. 修该该脚本的任务数
```
#SBATCH --ntasks 4
```
3. 修改执行的代码部分
```
$MPI_HOME/bin/mpiexec -n 4 -wdir $WORKPATH $WORKPATH/axisem3d
```
4. 如果报以下错误，则说明安装成功

```
Computing is started at Mon Jan 17 09:23:50 CST 2022.
!!!!!!!!!!!! AxiSEM3D ABORTED UPON RUNTIME EXCEPTION !!!!!!!!!!!
FROM: io::verifyDirectories
WHAT: Missing input directory:
      /home/lloyd/AxiSEM3D/build/input
TIME: 2022-01-17T09:23:55
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
Abort(1) on node 0 (rank 0 in comm 0): application called MPI_Abort(MPI_COMM_WORLD, 1) - process 0
Computing is stopped at Mon Jan 17 09:23:55 CST 2022.
```



## 修订历史

- 2022-01-17：初稿；

## 参考资料
- https://github.com/kuangdai/AxiSEM-3D/wiki/installation


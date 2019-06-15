---
title: Linux 安装 caffe
date: 2019-06-15
tags: ["caffe"]
author: Lloyd
categories: ["安装"]
slug: installCaffe
---

### 前言
CAFFE（快速特征嵌入的卷积结构，Convolutional Architecture for Fast Feature Embedding）是一个深度学习框架，最初开发于加利福尼亚大学伯克利分校。
我们按照官方的[说明网页](http://caffe.berkeleyvision.org/install_apt.html)来介绍如何在Ubuntu 16.04 LTS系统上手工编译安装该软件。

### 安装依赖包
#### 一般依赖项
```
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
sudo apt-get install --no-install-recommends libboost-all-dev
```
#### 特殊依赖软件

- CUDA 安装见另外一篇[博文](https://blog.nickwhyy.top/cuda/)

### 安装
#### 下载caffe
由于我安装caffe的目的是跑SSD代码，所以选择的是weiliu89的fork分支进行安装。
```
 $ git clone https://github.com/weiliu89/caffe.git
 $ cd caffe
 $ git checkout ssd
```
#### 编译
我们先尝试编译一下代码，然后在遇到问题的过程中逐步解决问题。
```
 # Modify Makefile.config according to your Caffe installation.
 $ cp Makefile.config.example Makefile.config
 $ make -j8
```
此时，可能会出现错误一：
>
`.build_release/src/caffe/proto/caffe.pb.h:9:42: fatal error: google/protobuf/stubs/common.h: No such file or directory`

错误一解决方案：
```
$ sudo apt-get install libprotobuf-dev protobuf-compiler
# 继续编译
$ make -j8
```
此时，可能会出现错误二：
>
`./include/caffe/common.hpp:5:27: fatal error: gflags/gflags.h: No such file or directory`

错误二解决方案：
```
$ sudo apt-get install libprotobuf-dev protobuf-compiler
# 继续编译
$ make -j8
```

此时，可能会出现错误三：
>
`./include/caffe/common.hpp:6:26: fatal error: glog/logging.h: No such file or directory`

错误三解决方案：
```
$ sudo apt-get install libgoogle-glog-dev
# 继续编译
$ make -j8
```
此时，可能会出现错误四：
>
`./include/caffe/util/device_alternate.hpp:34:23: fatal error: cublas_v2.h: No such file or directory`

错误四产生原因：
>
没有把cuda的头文件、库的路径放置到caffe的`Makefile.config`中

错误四解决方案：
在`Makefile.config`文件中添加下列两行，具体位置可放在`# If Homebrew is installed at a non standard location 
(for example your home directory) and you use it for general dependencies`这句话下面。
```
INCLUDE_DIRS += /usr/local/cuda-8.0/include
LIBRARY_DIRS += /usr/local/cuda-8.0/lib64
```
修改完后，继续编译`make -j8`

此时，可能会出现错误五：
>
`./include/caffe/util/mkl_alternate.hpp:14:19: fatal error: cblas.h: No such file or directory`

错误五解决方案：
```
$ sudo apt-get install libopenblas-dev
# 继续编译
$ make -j8
```

此时，可能会出现错误六：
> `src/caffe/layers/hdf5_data_layer.cpp:13:18: fatal error: hdf5.h: No such file or directory`

错误六解决方案：
第一步，先安装下面的依赖包
```
$ sudo apt install libhdf5-serial-dev
```
第二步，编辑`Makefile.config`文件
原文件为：
```
# Whatever else you find you need goes here.
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include 
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib 
```
现在修改为：
```
# Whatever else you find you need goes here.
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial/
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu/hdf5/serial/
```
第三步，编辑`Makefile`文件
原文件为：
```
LIBRARIES += glog gflags protobuf boost_system boost_filesystem m hdf5_hl hdf5
```
现修改为：
```
LIBRARIES += glog gflags protobuf boost_system boost_filesystem m hdf5_serial_hl hdf5_serial
```
修改完后，继续编译`make -j8`

此时，可能会出现错误七：
>
`./include/caffe/util/db_leveldb.hpp:7:24: fatal error: leveldb/db.h: No such file or directory`

错误七解决方案：
```
$ sudo apt-get install libleveldb-dev
# 继续编译
$ make -j8
```

此时，可能会出现错误八：
>
`./include/caffe/util/db_lmdb.hpp:8:18: fatal error: lmdb.h: No such file or directory`

错误八解决方案：
```
$ sudo apt-get install liblmdb-dev
# 继续编译
$ make -j8
```

此时，可能会出现错误九：
>
`/usr/bin/ld: cannot find -lsnappy`

错误九解决方案：
```
$ sudo apt-get install libsnappy-dev
# 继续编译
$ make -j8
```
这时候，基本上就可以成功编译了。

接着，继续执行下列命令：
```
$ make py
$ make test -j8
$ make runtest -j8
```

此时，可能会出现错误十：
>
`
.build_release/tools/caffe: error while loading shared libraries: libcudart.so.8.0: cannot open shared object file: No such file or directory

`

错误十解决方案：
在`~/.bashrc`文件中添加
```
export LIBRARY_PATH=$LIBRARY_PATH:/usr/local/cuda-8.0/lib64
```
之后再执行`make runtest -j8`，就会顺利地跑测试程序，不出意外的话，可得到如下结果：
```
$ make runtest -j8
.build_release/tools/caffe
caffe: command line brew
usage: caffe <command> <args>

commands:
  train           train or finetune a model
  test            score a model
  device_query    show GPU diagnostic information
  time            benchmark model execution time

  Flags from tools/caffe.cpp:
    -gpu (Optional; run in GPU mode on given device IDs separated by ','.Use
      '-gpu all' to run on all available GPUs. The effective training batch
      size is multiplied by the number of devices.) type: string default: ""
    -iterations (The number of iterations to run.) type: int32 default: 50
    -level (Optional; network level.) type: int32 default: 0
    -model (The model definition protocol buffer text file.) type: string
      default: ""
    -phase (Optional; network phase (TRAIN or TEST). Only used for 'time'.)
      type: string default: ""
    -sighup_effect (Optional; action to take when a SIGHUP signal is received:
      snapshot, stop or none.) type: string default: "snapshot"
    -sigint_effect (Optional; action to take when a SIGINT signal is received:
      snapshot, stop or none.) type: string default: "stop"
    -snapshot (Optional; the snapshot solver state to resume training.)
      type: string default: ""
    -solver (The solver definition protocol buffer text file.) type: string
      default: ""
    -stage (Optional; network stages (not to be confused with phase), separated
      by ','.) type: string default: ""
    -weights (Optional; the pretrained weights to initialize finetuning,
      separated by ','. Cannot be set simultaneously with snapshot.)
      type: string default: ""
.build_release/test/test_all.testbin 0 --gtest_shuffle 
Cuda number of devices: 1
Setting to use device 0
Current device id: 0
Current device name: GeForce GTX 1060 3GB
Note: Randomizing tests' orders with a seed of 17807 .
[==========] Running 2301 tests from 299 test cases.
[----------] Global test environment set-up.
[----------] 8 tests from LRNLayerTest/3, where TypeParam = caffe::GPUDevice<double>
[ RUN      ] LRNLayerTest/3.TestSetupWithinChannel
[       OK ] LRNLayerTest/3.TestSetupWithinChannel (1471 ms)
[ RUN      ] LRNLayerTest/3.TestSetupAcrossChannels
...
[----------] Global test environment tear-down
[==========] 2301 tests from 299 test cases ran. (372301 ms total)
[  PASSED  ] 2301 tests.
```
最后的`PASSED`表示全部测试都通过了，说明安装成功。

### 参考资料
- [官方安装文档](http://caffe.berkeleyvision.org/install_apt.html)
- [caffe编译的问题解决：“cublas_v2.h: No such file or directory”](https://blog.csdn.net/vbskj/article/details/52120475)
- [error 'google/protobuf/stubs/common.h'](https://github.com/tensorflow/models/issues/1116)
- [libcudart.so.8.0: cannot open shared object file](https://blog.csdn.net/u010454261/article/details/71268325)
- [ubuntu16.04+cuda8.0.44+cudnn5.1.5+caffe问题集](https://blog.csdn.net/zllljf/article/details/80980939)
- [WIKI](https://zh.wikipedia.org/wiki/Caffe)
- [github](https://github.com/weiliu89/caffe)

### 修订历史
 - 2019-06-15， 初稿

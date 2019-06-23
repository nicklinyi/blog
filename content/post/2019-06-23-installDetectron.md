---
title: 目标检测框架 Detectron 在 Linux 上的配置
date: 2019-06-23
tags: ["caffe2", "Pytorch", "Detectron"]
author: Lloyd
categories: ["安装"]
slug: installdetectron
---

### 前言
本文介绍如何在Ubuntu 16.04 LTS 系统上配置目标检测框架 Detectron。其他软件版本如下：

- Python 3.6
- CUDA 9.0
- cuDNN 7.3


Detectron 是 Facebook AI 研究组开发的一个实现了目前主流目标识别方法的软件，该软件基于 caffe2 框架上。而 caffe2 又集成于
PyTorch 中，所以需要安装PyTorch.  

### 安装PyTorch
建议手动编译PyTorch，整个过程比较简单。具体如下：

#### 安装依赖项
```
$ sudo apt-get update
$ sudo apt-get install -y --no-install-recommends \
      build-essential \
      git \
      libgoogle-glog-dev \
      libgtest-dev \
      libiomp-dev \
      libleveldb-dev \
      liblmdb-dev \
      libopencv-dev \
      libopenmpi-dev \
      libsnappy-dev \
      libprotobuf-dev \
      openmpi-bin \
      openmpi-doc \
      protobuf-compiler \
      python-dev \
      python-pip                          
$ pip install --user \
      future \
      numpy \
      protobuf \
      typing \
      hypothesis
# for Ubuntu 16.04
$ sudo apt-get install -y --no-install-recommends \
      libgflags-dev \
      cmake
```
#### 安装CUDA
参见另外一篇[博文](https://blog.nickwhyy.top/cuda/)

#### 复制Pytorch仓库代码并安装
```
$ git clone https://github.com/pytorch/pytorch.git && cd pytorch
$ git submodule update --init --recursive
$ python setup.py install
```

#### 测试caffe2是否安装成功
```
$ cd ~ && python -c 'from caffe2.python import core' 2>/dev/null && echo "Success" || echo "Failure"
```
如果现实`Success`则安装成功。

### 安装Detectron

#### 安装COCO API
```
$ export COCOAPI=/home/lloyd/cocoapi
$ git clone https://github.com/cocodataset/cocoapi.git $COCOAPI
$ cd $COCOAPI/PythonAPI
$ make install
$ python setup.py install --user
```

#### Detectron
```
$ export DETECTRON=/home/lloyd/detectron
$ git clone https://github.com/facebookresearch/detectron $DETECTRON
$ pip install -r $DETECTRON/requirements.txt
$ cd $DETECTRON && make
```

#### 测试
```
$ python $DETECTRON/detectron/tests/test_spatial_narrow_as_op.py
```

### 错误汇总
1.
> 
`ValueError: numpy.ufunc size changed, may indicate binary incompatibility. Expected 216 from C header, got 192 from PyObject`

原因，`numpy`版本问题，经测试`numpy==1.16.1`版本不会出现该问题。

2.
>
`ImportError: numpy.core.multiarray failed to import`

原因仍是`numpy`版本问题，经测试`numpy==1.16.1`版本不会出现该问题。

3.
>
```
yaml.constructor.ConstructorError: while constructing a Python instance
expected a class, but found <class 'builtin_function_or_method'>
```
解决方案：
在`detectron/utils/env.py`文件中将`yaml_load = yaml.load`改为
`yaml_load = lambda x: yaml.load(x, Loader=yaml.Loader)`即可。


### 参考资料
- [caffe2官网](https://caffe2.ai/docs/getting-started.html?platform=ubuntu&configuration=prebuilt#install-with-gpu-support)
- [ValueError: numpy.ufunc size changed)](https://github.com/scikit-image/scikit-image/issues/3655)
- [yaml.constructor.ConstructorError](https://github.com/facebookresearch/Detectron/issues/840)


### 修订历史
 - 2019-06-23， 初稿

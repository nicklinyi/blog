---
title: CentOS 7.8 源码编译 tensorflow 2.5
date: 2021-01-10
tags: ["tensorflow"]
author: Lloyd
categories: ["安装"]
slug: insTFnoRoot
---

### 前言
之前写过一篇关于在Windows 10上安装tensorflow的例子，这篇博文主要讨论如何在没有root权限的服务器的GPU节点上源码编译tensorflow.

### 安装TensorFlow pip 软件包依赖项：

```bash
$ pip install -U --user pip six numpy wheel setuptools mock 'future>=0.17.1'
$ pip install -U --user keras_applications --no-deps
$ pip install -U --user keras_preprocessing --no-deps
```

### 安装Bazel：

#### 下载Bazel

Bazel 版本必须在`tensorflow/configure.py`中指定的介于`_TF_MIN_BAZEL_VERSION`和`_TF_MAX_BAZEL_VERSION`之间的任一版本。

从github上的Bazel项目上下载[bazel-3.7.2-installer-linux-x86_64.sh](https://github.com/bazelbuild/bazel/releases/download/3.7.2/bazel-3.7.2-installer-linux-x86_64.sh).

#### 安装Bazel

```bash
$ bash bazel-3.7.2-installer-linux-x86_64.sh --prefix=~/soft/bazel-3.7.2
```

### 安装tensorflow

#### 下载tensorflow

```bash
$ git clone https://github.com/tensorflow/tensorflow.git
```

#### 配置build

tensorflow 要求gcc的版本大于4.9，因此，可以在gpu节点上先enable gcc9的环境。

```bash
$ scl enable devtoolset-9 bash
```

配置build

```bash
$ ./configure
```

选择gcc9的环境，python3的环境，选择cuda，tensorRT不需要。

#### 编译

```bash
$ bazel build --config=v2 --config=opt --config=cuda //tensorflow/tools/pip_package:build_pip_package
```

编译大概需要16 h

#### 构建whl

```bash
$ ./bazel-bin/tensorflow/tools/pip_package/build_pip_package --nightly_flag /tmp/tensorflow_pkg
```

#### 安装软件包

```bash
$ pip install /tmp/tensorflow_pkg/tensorflow-version-tags.whl
```

### 报错

#### gcc 

```
gcc: error: unrecognized command line option '-std=c++14'
```

主要原因是服务器的默认gcc版本是4.8.5， 采用scl 添加完gcc9的环境后，需要重新configure。

#### git

```
fatal: unable to find remote helper for 'https'
```

把登陆节点的`/usr/libexec/git-core` 和`/usr/bin/git`都纳入到自己的PATH中，即可解决这个问题。

### 参考资料
- [官方安装说明](https://www.tensorflow.org/install/source?hl=zh-cn)

### 修订历史
 - 2021-01-10， 初稿

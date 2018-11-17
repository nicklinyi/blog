---
title: Windows 10 安装 tensorflow 1.4.0
date: 2018-11-17
tags: ["tensorflow"]
author: Lloyd
categories: ["安装"]
slug: installTensorFlow
---

### 前言
本文在本博文之前，已经有很多博文介绍了如何在 Windows10 系统中安装 GPU 版的 tensorflow, 但这些文章存在各种各样的缺陷，完全按照他们的文章，你不一定能
成功安装 tensorflow。基于此，本文旨在用更科学的方式指导大家如何在 Windows 10 系统中安装 GPU 版的 tensorflow，这里安装的不是最新版的，原因下面会提
到。

### 前期准备
tensorflow 从发布到现在(写此文的时间)已经经历过多次的升级，而与此同时， NVIDIA 公司开发的 CUDA 以及 cuDNN 这两个依赖项也进行了更新。因此，就导致了
不同版本的 tensorflow 会依赖于不同版本 的 CUDA 和 cuDNN，具体见下表(https://www.tensorflow.org/install/source_windows?hl=zh-cn)。

![tensorflow-gpu.PNG](https://upload-images.jianshu.io/upload_images/1703880-6739053ccdf29cc3.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里插播一下我选择安装 tensorflow 1.4.0 的原因，并不是说该版本最稳定，毕竟我也是第一次用 tensorflow ，在此没有话语权。在安装 tensorflow 的时候，我
还不知道有这个表格，我甚至都不知道还要安装 CUDA 和 cuDNN。我上来就知道用 `pip install tensorflow-gpu`，安装的过程中也没报错，我就天真的以为安装成
功了。接着，用官方提供的简单例子跑了一下，才提示我`ImportError: DLL load failed: 找不到指定的模块。`. 然后，我搜索了一下问题，发现我没有装 CUDA。
然后我就上 NVIDIA 官网，下载并安装了最新的 CUDA 10。安装，运行例子，还是同样的错误。然后，我搜索了一下问题，发现我没有装 cuDNN。接着，我就上 NVIDIA
官网，下载并安装了 cuDNN。然而，还是没解决该问题。后来经过一次偶然的发现，找到了一个算是半官方的一个用来检测原因的脚本
(https://gist.github.com/mrry/ee5dbcfdd045fa48a27d56664411d41c)，经过测试还知道有不同版本的 tensorflow 会依赖于不同版本 的 CUDA 和 cuDNN 这一
说。但我当时并知道有上面这个表格，所以只能瞎试了一通。等我发现有这个表格的时候，我电脑里 CUDA 的版本是 8，cuDNN 的版本是 6，因此符合条件的 tensorflow
的版本是 1.4.0 和 1.3.0。一般新版本比旧版本肯定是更新了新的功能，修复了一定的bug。所以我就选择了 1.4.0 。建议大家直接安装 1.12.0 。

各种依赖项在 windows 下安装都比较简单，在此我不再赘述如何具体一步步安装了。不过，我提供一些我觉得讲的比较细致的安装策略供大家参考。
#### CUDA
参见 https://zhuanlan.zhihu.com/p/30528874

#### cuDNN
参见 https://zhuanlan.zhihu.com/p/30528874

#### Visual studio 2015
参见 https://zhuanlan.zhihu.com/p/30528874

#### MSVC 2015 update3
下载地址 https://www.microsoft.com/en-us/download/confirmation.aspx?id=53587，选择自己对应的版本和系统位数，然后下载，安装即可。

#### Anaconda
参见 https://zhuanlan.zhihu.com/p/30528874

添加 conda 镜像源：https://wiki.seisman.info/python/install-python/
添加 pip 镜像源：https://wiki.seisman.info/python/install-python/

### 安装 tensorflow 1.4.0
查看表格我们知道，tensorflow 1.4.0 支持的 Python 版本为 3.5-3.6, 我本人选择的是 3.5.4 的版本。因此，打开 Anaconda Prompt, 输入下面命令

```
conda create -n tensorflow-gpu-py35 python=3.5
```

创建一个 Python 3.5 的环境，然后激活该环境，

```
activate tensorflow
```

接着安装 tensorflow 1.4.0,并制定具体的版本号

```
pip install tensorflow-gou==1.4.0
```

下面显示了一个成功安装的案例：

```
(tensorflow) C:\Users\Admin>pip install tensorflow-gpu==1.4.0
Collecting tensorflow-gpu==1.4.0
  Downloading https://files.pythonhosted.org/packages/0a/0d/1a52e775e490f2fcb0eba08b3df773e6e6d64934c77346b351f6df2ed8df/tensorflow_gpu-1.4.0-cp35-cp35m-win_amd64.whl (67.6MB)
    100% |████████████████████████████████| 67.6MB 336kB/s
Requirement already satisfied: six>=1.10.0 in g:\program\conda\envs\tensorflow\lib\site-packages (from tensorflow-gpu==1.4.0) (1.11.0)
Requirement already satisfied: numpy>=1.12.1 in g:\program\conda\envs\tensorflow\lib\site-packages (from tensorflow-gpu==1.4.0) (1.15.4)
Collecting tensorflow-tensorboard<0.5.0,>=0.4.0rc1 (from tensorflow-gpu==1.4.0)
  Downloading https://files.pythonhosted.org/packages/e9/9f/5845c18f9df5e7ea638ecf3a272238f0e7671e454faa396b5188c6e6fc0a/tensorflow_tensorboard-0.4.0-py3-none-any.whl (1.7MB)
    100% |████████████████████████████████| 1.7MB 2.9MB/s
Collecting enum34>=1.1.6 (from tensorflow-gpu==1.4.0)
  Downloading https://files.pythonhosted.org/packages/af/42/cb9355df32c69b553e72a2e28daee25d1611d2c0d9c272aa1d34204205b2/enum34-1.1.6-py3-none-any.whl
Requirement already satisfied: wheel>=0.26 in g:\program\conda\envs\tensorflow\lib\site-packages (from tensorflow-gpu==1.4.0) (0.32.2)
Requirement already satisfied: protobuf>=3.3.0 in g:\program\conda\envs\tensorflow\lib\site-packages (from tensorflow-gpu==1.4.0) (3.6.1)
Requirement already satisfied: markdown>=2.6.8 in g:\program\conda\envs\tensorflow\lib\site-packages (from tensorflow-tensorboard<0.5.0,>=0.4.0rc1->tensorflow-gpu==1.4.0) (3.0.1)
Collecting html5lib==0.9999999 (from tensorflow-tensorboard<0.5.0,>=0.4.0rc1->tensorflow-gpu==1.4.0)
  Downloading https://files.pythonhosted.org/packages/ae/ae/bcb60402c60932b32dfaf19bb53870b29eda2cd17551ba5639219fb5ebf9/html5lib-0.9999999.tar.gz (889kB)
    100% |████████████████████████████████| 890kB 2.8MB/s
Requirement already satisfied: werkzeug>=0.11.10 in g:\program\conda\envs\tensorflow\lib\site-packages (from tensorflow-tensorboard<0.5.0,>=0.4.0rc1->tensorflow-gpu==1.4.0) (0.14.1)
Collecting bleach==1.5.0 (from tensorflow-tensorboard<0.5.0,>=0.4.0rc1->tensorflow-gpu==1.4.0)
  Downloading https://files.pythonhosted.org/packages/33/70/86c5fec937ea4964184d4d6c4f0b9551564f821e1c3575907639036d9b90/bleach-1.5.0-py2.py3-none-any.whl
Requirement already satisfied: setuptools in g:\program\conda\envs\tensorflow\lib\site-packages (from protobuf>=3.3.0->tensorflow-gpu==1.4.0) (40.6.2)
Building wheels for collected packages: html5lib
  Running setup.py bdist_wheel for html5lib ... done
  Stored in directory: C:\Users\Admin\AppData\Local\pip\Cache\wheels\50\ae\f9\d2b189788efcf61d1ee0e36045476735c838898eef1cad6e29
Successfully built html5lib
Installing collected packages: html5lib, bleach, tensorflow-tensorboard, enum34, tensorflow-gpu
Successfully installed bleach-1.5.0 enum34-1.1.6 html5lib-0.9999999 tensorflow-gpu-1.4.0 tensorflow-tensorboard-0.4.0
```

### 测试
下面是我测试的情况：

```
(tensorflow) C:\Users\Admin>python
Python 3.5.4 |Continuum Analytics, Inc.| (default, Aug 14 2017, 13:41:13) [MSC v.1900 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import tensorflow
>>> import tensorflow as tf
>>> hello = tf.constant("Hello! Tensorflow")
>>> sess = tf.Session()
2018-11-17 11:01:26.751687: I C:\tf_jenkins\home\workspace\rel-win\M\windows-gpu\PY\35\tensorflow\core\platform\cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX AVX2
2018-11-17 11:01:27.021386: I C:\tf_jenkins\home\workspace\rel-win\M\windows-gpu\PY\35\tensorflow\core\common_runtime\gpu\gpu_device.cc:1030] Found device 0 with properties:
name: GeForce GTX 1060 3GB major: 6 minor: 1 memoryClockRate(GHz): 1.7465
pciBusID: 0000:01:00.0
totalMemory: 3.00GiB freeMemory: 2.46GiB
2018-11-17 11:01:27.034342: I C:\tf_jenkins\home\workspace\rel-win\M\windows-gpu\PY\35\tensorflow\core\common_runtime\gpu\gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: GeForce GTX 1060 3GB, pci bus id: 0000:01:00.0, compute capability: 6.1)
>>> print(sess.run(hello))
b'Hello! Tensorflow'
>>> quit()
```


### 参考资料
- [前期准备安装](https://zhuanlan.zhihu.com/p/30528874)
- [Anaconda和pip配置](https://wiki.seisman.info/python/install-python/)
- [不同版本tensorflow和其所对应的CUDA和cuDNN](https://www.tensorflow.org/install/source_windows?hl=zh-cn)



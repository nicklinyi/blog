---
title: 目标检测算法 SSD 在 Linux 上的配置
date: 2019-06-16
tags: ["caffe", "SSD"]
author: Lloyd
categories: ["安装"]
slug: installssd
---

### 前言
本文介绍如何在Ubuntu 16.04 LTS 系统上配置目标检测框架 SSD。其他软件版本如下：

- Python 3.6
- CUDA 8.0

由于 SSD 是在 caffe 框架上建立的，所以得先安装 caffe， 具体见另一篇[博文](https://blog.nickwhyy.top/installcaffe/). 
成功安装caffe后，我们进入到 SSD 的配置。

### 数据准备
1. 下载 [fully convolutional reduced (atrous) VGGNet](https://gist.github.com/weiliu89/2ed6e13bfd5b57cf81d6). 
并将其存储于`$CAFFE_ROOT/models/VGGNet/`下。
2. 下载 VOC2007 和 VOC2012 数据集. 并将其存储于 `$HOME/data/`下。
```
  # 下载数据集
 $ cd $HOME/data
 $ wget http://host.robots.ox.ac.uk/pascal/VOC/voc2012/VOCtrainval_11-May-2012.tar
 $ wget http://host.robots.ox.ac.uk/pascal/VOC/voc2007/VOCtrainval_06-Nov-2007.tar
 $ wget http://host.robots.ox.ac.uk/pascal/VOC/voc2007/VOCtest_06-Nov-2007.tar
  # 解压缩
 $ tar -xvf VOCtrainval_11-May-2012.tar
 $ tar -xvf VOCtrainval_06-Nov-2007.tar
 $ tar -xvf VOCtest_06-Nov-2007.tar
```
3. 创建LMDB文件。
```
$  cd $CAFFE_ROOT
  # Create the trainval.txt, test.txt, and test_name_size.txt in data/VOC0712/
$  ./data/VOC0712/create_list.sh
  # You can modify the parameters in create_data.sh if needed.
  # It will create lmdb files for trainval and test with encoded original image:
  #   - $HOME/data/VOCdevkit/VOC0712/lmdb/VOC0712_trainval_lmdb
  #   - $HOME/data/VOCdevkit/VOC0712/lmdb/VOC0712_test_lmdb
  # and make soft links at examples/VOC0712/
$  ./data/VOC0712/create_data.sh
```
其中，`create_data.sh`这一步可能会报错，内容如下

>
`ImportError: No module named caffe.proto`

解决方案：
在`~/.bashrc`文件中添加下面两行
```
export CAFFE_ROOT=$HOME/caffe # 按照自己caffe的安装目录填写即可
export PYTHONPATH=$CAFFE_ROOT/python:$PYTHONPATH
```
然后，接着执行`create_data.sh`,可能会报如下错误：

>
`ImportError: dynamic module does not define module export function (PyInit__caffe)`

解决方案：
这问题是编译caffe时所选的版本和自己目前环境变量中python版本不一致导致的，需要重新编译caffe。修改
`Makefile.config`文件
```
# 注释掉Python2.7相关的语句
#PYTHON_INCLUDE := /usr/include/python2.7 \
#		/usr/lib/python2.7/dist-packages/numpy/core/include

# 选择 anaconda python3.6， 注意这里选择自己对应的anaconda的安装位置，以及自己anaconda虚拟环境
所创建的python 3.6的名称
ANACONDA_HOME := $(HOME)/anaconda3/envs/py36
PYTHON_INCLUDE := $(ANACONDA_HOME)/include \
		 $(ANACONDA_HOME)/include/python3.6m \
		 $(ANACONDA_HOME)/lib/python3.6/site-packages/numpy/core/include

# 更改PYTHON_LIB
#PYTHON_LIB := /usr/lib
 PYTHON_LIB := $(ANACONDA_HOME)/lib
```
修改之后，重新编译caffe
```
$ make clean
$ make -j8
$ make py
# 运行 create_data.sh
$ ./data/VOC0712/create_data.sh
```
此时，可能会出现新的错误：

>
`
ImportError: /home/lloyd/caffe/python/caffe/_caffe.so: 
undefined symbol: _ZN5boost6python6detail11init_moduleER11PyModuleDefPFvvE
`

错误产生原因：
`boost_python`的版本不匹配，使用的python版本为3.6，而`/usr/lib/x86_64-linux-gnu/`下没有对应
python 3.6版本的库文件。

解决方案：
安装boost_python,采用python3.6的环境进行编译。
```
# 下载安装包
wget -O boost_1_67_0.tar.gz http://sourceforge.net/projects/boost/files/boost/1.67.0/boost_1_67_0.tar.gz/download  
# 解压缩
tar xzvf boost_1_67_0.tar.gz      
# 进入boost目录
cd boost_1_67_0/  
# configure
./bootstrap.sh --prefix=/home/lloyd/boost --with-python=/home/lloyd/anaconda3/envs/py36/bin/python3 --with-python-root=/home/lloyd/anaconda3/envs/py36 --with-python-version=3.6
```
修改`project-config.jam`文件
原文件为：
```
# Python configuration
import python ;
if ! [ python.configured ]
{
    using python : 3.6 : /home/lloyd/anaconda3/envs/py36 ;
}
```
修改后为：
```
# Python configuration
import python ;
if ! [ python.configured ]
{
    using python : 3.6 : /home/lloyd/anaconda3/envs/py36 : /home/lloyd/anaconda3/envs/py36/include/python3.6m : /home/lloyd/anaconda3/envs/py36/lib ;
}
```
然后修改`tools/build/src/tools/python.jam`文件第547行，
原文件为：
```
includes ?= $(prefix)/include/python$(version) ;
```
修改为：
```
includes ?= $(prefix)/include/python$(version)m ;
```
之后，编译，安装即可：
```
$ ./b2 
$ ./b2 install
# 复制库文件到系统库文件，建立软链接
$ sudo cp /home/lloyd/boost/lib/libboost_python36.a  /usr/lib/x86_64-linux-gnu/libboost_python_python36.a
$ sudo cp /home/lloyd/boost/lib/libboost_python36.so.1.67.0  /usr/lib/x86_64-linux-gnu/libboost_python36.so.1.67.0
$ sudo cp /home/lloyd/boost/lib/libboost_python36.so /usr/lib/x86_64-linux-gnu/libboost_python36.so
$ sudo ln /usr/lib/x86_64-linux-gnu/libboost_python36.so /usr/lib/x86_64-linux-gnu/libboost_python3.so
```
接着，回到`$CAFFE_ROOT`，然后继续运行`create_data.sh`,就能正确地创建数据文件了。


### 训练
首先修改`examples/ssd/ssd_pascal.py`一些与数据存储位置相关的参数以及GPU信息。然后运行下面的命令
```
$ python examples/ssd/ssd_pascal.py
```
可能出现如下错误：

>
  `File "examples/ssd/ssd_pascal.py", line 314, in <module>
    for ratio in xrange(min_ratio, max_ratio + 1, step):
NameError: name 'xrange' is not defined`

主要原因是在Python 3中，`range()`与`xrange()`合并为`range()`。因此修改`examples/ssd/ssd_pascal.py`
中的`xrange`为`range`即可。

然后继续运行`python examples/ssd/ssd_pascal.py`,可能出现的错误有：

>
`assert len > 0
TypeError: '>' not supported between instances of 'builtin_function_or_method' and 'int'`

修改策略是注释掉`model_libs.py`中函数`UnpackVariable`定义中与`assert`相关的语句。

然后继续运行`python examples/ssd/ssd_pascal.py`,可能出现的错误有：

>
`getattr(proto, name).extend(val)
TypeError: 1.0 has type float, but expected one of: int, long`

原因是在python3中 `/` 的结果是 float 导致的.
解决方法：
修改`python/caffe/net_spec.py`文件中含有`dilation`且含有`/`的语句，一共三条。比如：
```
# pad = int((3 + (dilation - 1) * 2) - 1) / 2 # 原文件
pad = int((3 + (dilation - 1) * 2) - 1) // 2  # 修改后
```
按照此策略，依次修改掉其他的另外两行语句。

然后继续运行`python examples/ssd/ssd_pascal.py`，就能顺利训练数据了。

### 参考资料
- [SSD官方repo](https://github.com/weiliu89/caffe)
- [ImportError: No module named caffe.proto](https://blog.csdn.net/lanyuelvyun/article/details/73628152)
- [ImportError: dynamic module does not define module export function (PyInit__caffe)](https://github.com/BVLC/caffe/issues/6054)
- [undefined symbol: _ZN5boost6python6detail11init_moduleER11PyModuleDefPFvvE](https://blog.csdn.net/sinat_35406909/article/details/84198140)
- [boost_python3 安装](https://www.cnblogs.com/freeweb/p/9209362.html)
- [cannot find -lboost_python3](https://github.com/BVLC/caffe/issues/4843)
- [boost_python3 配置](https://www.jianshu.com/p/5afdb561ce94)
- [boost1.65.1库编译报错，找不到 pyconfig.h](https://blog.csdn.net/lianchenglian/article/details/77945499)
- [getattr(proto, name).extend(val)](https://blog.csdn.net/dihe874981/article/details/86622906)

### 修订历史
 - 2019-06-16， 初稿

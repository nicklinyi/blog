---
title: Windows 10 安装 Keras 2.2.4
date: 2019-05-17
tags: ["keras"]
author: Lloyd
categories: ["安装"]
slug: installKeras
---

### 前期准备
Keras 是一个用Python 编写的高级神经网络API，它能够以TensorFlow, CNTK, 或者Theano 作为后端运行。在此安装以Tensorflow为后端的Keras。

### 安装 tensorflow 1.5.0
并不是说Keras 2.2.4版本一定要安装tensorflow 1.5.0，只不过是我之前装好了tensorflow-1.5.0，并在此基础上成功的安装了Keras 2.2.4. 所以
这两个版本在我电脑上是能正常工作的。安装tensorflow 1.5.0 可参考我的另外一篇博文https://blog.nickwhyy.top/installtensorflow/

### 安装 keras 2.2.4

```
(tensorflow) C:\Users\Admin>pip install keras -U --pre
Collecting keras
  Using cached https://files.pythonhosted.org/packages/5e/10/aa32dad071ce52b5502266b5c659451cfd6ffcbf14e6c8c4f16c0ff5aaab/Keras-2.2.4-py2.py3-none-any.whl
Requirement already up-to-date: six>=1.9.0 in g:\program\anaconda3\envs\tensorflow-gpu-py35\lib\site-packages (from keras)
Requirement already up-to-date: numpy>=1.9.1 in g:\program\anaconda3\envs\tensorflow-gpu-py35\lib\site-packages (from keras)
Requirement already up-to-date: h5py in g:\program\anaconda3\envs\tensorflow-gpu-py35\lib\site-packages (from keras)
Requirement already up-to-date: keras-applications>=1.0.6 in g:\program\anaconda3\envs\tensorflow-gpu-py35\lib\site-packages (from keras)
Requirement already up-to-date: keras-preprocessing>=1.0.5 in g:\program\anaconda3\envs\tensorflow-gpu-py35\lib\site-packages (from keras)
Requirement already up-to-date: pyyaml in g:\program\anaconda3\envs\tensorflow-gpu-py35\lib\site-packages (from keras)
Requirement already up-to-date: scipy>=0.14 in g:\program\anaconda3\envs\tensorflow-gpu-py35\lib\site-packages (from keras)
Installing collected packages: keras
Successfully installed keras-2.2.4
```
验证是否能运行，进入python，并import keras 模块

```
(tensorflow) C:\Users\Admin>python
Python 3.5.4 |Continuum Analytics, Inc.| (default, Aug 14 2017, 13:41:13) [MSC v.1900 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import keras
Using TensorFlow backend.
```
如果是类似上面的结果，表面keras安装成功。

### 测试
首先，先安装git，然后下载mnist数据集，接着运行例子脚本
```
(tensorflow) C:\Users\Admin>conda install git
(tensorflow) C:\Users\Admin>git clone https://github.com/fchollet/keras.git
(tensorflow) C:\Users\Admin>cd keras/examples/
(tensorflow) C:\Users\Admin>python mnist_mlp.py
Using TensorFlow backend.
60000 train samples
10000 test samples
_________________________________________________________________
Layer (type)                 Output Shape              Param #
=================================================================
dense_1 (Dense)              (None, 512)               401920
_________________________________________________________________
dropout_1 (Dropout)          (None, 512)               0
_________________________________________________________________
dense_2 (Dense)              (None, 512)               262656
_________________________________________________________________
dropout_2 (Dropout)          (None, 512)               0
_________________________________________________________________
dense_3 (Dense)              (None, 10)                5130
=================================================================
Total params: 669,706
Trainable params: 669,706
Non-trainable params: 0
_________________________________________________________________
Train on 60000 samples, validate on 10000 samples
Epoch 1/20
2019-05-17 08:33:14.461356: I C:\tf_jenkins\workspace\rel-win\M\windows-gpu\PY\35\tensorflow\core\platform\cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX AVX2
2019-05-17 08:33:14.733043: I C:\tf_jenkins\workspace\rel-win\M\windows-gpu\PY\35\tensorflow\core\common_runtime\gpu\gpu_device.cc:1105] Found device 0 with properties:
name: GeForce GTX 1060 3GB major: 6 minor: 1 memoryClockRate(GHz): 1.7465
pciBusID: 0000:01:00.0
totalMemory: 3.00GiB freeMemory: 2.42GiB
2019-05-17 08:33:14.737722: I C:\tf_jenkins\workspace\rel-win\M\windows-gpu\PY\35\tensorflow\core\common_runtime\gpu\gpu_device.cc:1195] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: GeForce GTX 1060 3GB, pci bus id: 0000:01:00.0, compute capability: 6.1)
60000/60000 [==============================] - 5s 88us/step - loss: 0.2495 - acc: 0.9227 - val_loss: 0.1002 - val_acc: 0.9672
Epoch 2/20
60000/60000 [==============================] - 3s 45us/step - loss: 0.1028 - acc: 0.9685 - val_loss: 0.0910 - val_acc: 0.9736
Epoch 3/20
60000/60000 [==============================] - 3s 44us/step - loss: 0.0750 - acc: 0.9770 - val_loss: 0.0776 - val_acc: 0.9765
Epoch 4/20
60000/60000 [==============================] - 3s 44us/step - loss: 0.0594 - acc: 0.9822 - val_loss: 0.0676 - val_acc: 0.9813
Epoch 5/20
60000/60000 [==============================] - 3s 43us/step - loss: 0.0502 - acc: 0.9848 - val_loss: 0.0753 - val_acc: 0.9802
Epoch 6/20
60000/60000 [==============================] - 3s 43us/step - loss: 0.0437 - acc: 0.9866 - val_loss: 0.0826 - val_acc: 0.9785
Epoch 7/20
60000/60000 [==============================] - 3s 43us/step - loss: 0.0397 - acc: 0.9885 - val_loss: 0.0767 - val_acc: 0.9808
Epoch 8/20
60000/60000 [==============================] - 3s 44us/step - loss: 0.0343 - acc: 0.9900 - val_loss: 0.0739 - val_acc: 0.9831
Epoch 9/20
60000/60000 [==============================] - 3s 44us/step - loss: 0.0310 - acc: 0.9907 - val_loss: 0.0893 - val_acc: 0.9806
Epoch 10/20
60000/60000 [==============================] - 3s 44us/step - loss: 0.0306 - acc: 0.9911 - val_loss: 0.0877 - val_acc: 0.9816
Epoch 11/20
60000/60000 [==============================] - 3s 44us/step - loss: 0.0280 - acc: 0.9923 - val_loss: 0.0829 - val_acc: 0.9836
Epoch 12/20
60000/60000 [==============================] - 3s 43us/step - loss: 0.0268 - acc: 0.9922 - val_loss: 0.0943 - val_acc: 0.9804
Epoch 13/20
60000/60000 [==============================] - 3s 44us/step - loss: 0.0235 - acc: 0.9932 - val_loss: 0.0942 - val_acc: 0.9821
Epoch 14/20
60000/60000 [==============================] - 3s 44us/step - loss: 0.0203 - acc: 0.9938 - val_loss: 0.1041 - val_acc: 0.9819
Epoch 15/20
60000/60000 [==============================] - 3s 43us/step - loss: 0.0223 - acc: 0.9937 - val_loss: 0.1115 - val_acc: 0.9808
Epoch 16/20
60000/60000 [==============================] - 3s 44us/step - loss: 0.0204 - acc: 0.9945 - val_loss: 0.1017 - val_acc: 0.9835
Epoch 17/20
60000/60000 [==============================] - 3s 44us/step - loss: 0.0197 - acc: 0.9950 - val_loss: 0.1142 - val_acc: 0.9829
Epoch 18/20
60000/60000 [==============================] - 3s 43us/step - loss: 0.0206 - acc: 0.9951 - val_loss: 0.1265 - val_acc: 0.9803
Epoch 19/20
60000/60000 [==============================] - 3s 44us/step - loss: 0.0196 - acc: 0.9950 - val_loss: 0.1015 - val_acc: 0.9842
Epoch 20/20
60000/60000 [==============================] - 3s 44us/step - loss: 0.0200 - acc: 0.9952 - val_loss: 0.1010 - val_acc: 0.9840
Test loss: 0.10101144369584511
Test accuracy: 0.984
```
如果出现上述的结果，就表明整个keras是可以正常运行的。如果出现`softmax() got an unexpected keyword argument 'axis'`这个错误，表明tensorflow
的版本过低，建议升级一下tensorflow，再重新安装keras。

### 参考资料
- [Keras windows安装文档](https://keras-cn.readthedocs.io/en/latest/for_beginners/keras_windows/)
- [softmax() error](https://github.com/keras-team/keras/issues/9621)

### 修订历史
 - 2019-05-17， 初稿


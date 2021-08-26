---
title: Madagascar Matplotlib 绘图指南
date: 2018-08-21
tags: ["Madagascar","Matplotlib","绘图"]
author: Lloyd
categories: ["绘图"]
slug: rsfpyplot
---

## 前言

Madagascar 本身自带了绘图功能，但其绘图字体并非主流杂志所描述的 Helvetica 或 Arial 字体，虽然官方给出了关于使用这些字体的
[博客](http://ahay.org/blog/2008/10/15/a-journal-requires-a-particular-font-e-g-arial-or-helvetica-in-figure-labels-how-do-i-achieve-that/)，
但实际上，大家仔细比较，会发现其绘出的字体和 Helvetica 或 Arial 字体有很大的差别。

基于此，我重新写了基于 Matplotlib 的绘图程序，目前的功能比较简单，只支持绘制类似于 Madagascar sfgrey 命令的图片，后面将继续增加更多的绘图 API，以丰富
Madagascar 的绘图效果。

采用 Matplotlib 绘图有以下好处

- 符合 Geophysics 等主流杂志的图片要求；
- 解决 Madagascar sfgraph 命令无法绘制 legend 的缺陷；
- 可直接生成各种图片格式，无需再进行各种转换。
- etc

## 依赖项

- 需要安装 Madagascar 的 Python API；
- 需要自行下载并安装 Helvetica 字体。

## 绘制 Image
1. 下载[源程序](https://github.com/nicklinyi/rsf/blob/master/Mgray.py)，并将其安装至 Madagascar 中。
2. 生成一个数据体，并进行绘图。可采用如下示例脚本

```python
from rsf.proj import *

Flow('vb',None,'math n1=101 d1=2 n2=201 d2=2 output="1500+25*x1"')

# gray image
Flow('vb.png','vb',
    'gray savefile=$TARGET xlabel="x (m)" ylabel="z (m)" cmap=gray ',stdout=-1)
    
# color image
Flow('vb_color.png','vb',
    'gray savefile=$TARGET xlabel="x (m)" ylabel="z (m)" cmap=jet ',stdout=-1)

End()
```
绘图结果：
![vb.png](https://i.postimg.cc/xCS47xc9/model1.png)
![vb_color.png](https://i.postimg.cc/Ss7vp6MC/model1-color.png)


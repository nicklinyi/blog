---
title: 地震子波
date: 2018-04-27 13:08:28
author: Lloyd
categories: [地震学]
tags: [地震子波]
mathjax: true
---

## 前言
主要记录下常用各种子波函数以及对应的图形。

## Ricker子波
$ S(t)=(1-2\pi^2f^2t^2)exp(-\pi^2f^2t^2) $

```
function [w] = ricker(fpeak,t,tdelay)
w = exp(-pi^2*fpeak^2*(t-tdelay).^2).*(1-2*pi^2*fpeak^2*(t-tdelay).^2);
return
```

<!-- more -->

![ricker.png](https://upload-images.jianshu.io/upload_images/1703880-f71641eca2730b1f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Ricker子波的一阶导数：
$$S(t)=2\pi^2f^2t(2\pi^2f^2t^2-3)exp(-\pi^2f^2t^2)$$

```
function [w] = ricker1(fpeak,t,tdelay)
 w = 2*pi^2*fpeak^2*(t-tdelay).*(2*pi^2*fpeak^2*(t-tdelay).^2-3).*...
     exp(-pi^2*fpeak^2*(t-tdelay).^2);
return
```
![ricker1.png](https://upload-images.jianshu.io/upload_images/1703880-4bd0b7444b017859.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 最小相位子波
$$S(t)=texp(-3.5ft)sin(2\pi ft)$$

```
function [w] = min_phase(fpeak,t,tdelay)
w = (t-tdelay).*exp(-3.5*fpeak*(t-tdelay)).*sin(2*pi*fpeak*(t-tdelay));
return
```
![min_phase.png](https://upload-images.jianshu.io/upload_images/1703880-75498f8321aee3f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## Gauss子波的导数：
$$S(t)=-A\pi^2f^2texp(-\pi^2f^2t^2)(3-2\pi^2f^2t^2)$$
```
function [w] = gauss1(fpeak,t,tdelay)
w =-pi^2*fpeak^2*(t-tdelay).*exp(-pi^2*fpeak^2*(t-tdelay).^2).*...
    (3-2*pi^2*fpeak^2*(t-tdelay).^2);
return
```


![gauss1.png](https://upload-images.jianshu.io/upload_images/1703880-1b1a9880ca8c6b3f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 参考资料
罗焕宏.基于单程波算子的地震波场模拟及叠前深度偏移[D].中国石油大学,2010



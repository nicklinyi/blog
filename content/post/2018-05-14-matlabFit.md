---
title: Matlab 拟合数据体类型解读
date: 2018-05-14
tags: [Matlab]
author: Lloyd
categories: [编程]
---



首先我们先针对一些列的散点(x1,y1)进行拟合(x1,y1均为矢量)。其三次样条拟合的结果如下图所示，并将
其拟合结果存为`fitresult`。

![拟合曲线](https://i.postimg.cc/SsKSkkrV/fit.png)


matlab 对数据做完三次样条拟合后的数据为`cfit`类型，是一种拟合的数据体，虽然此数据体可直接通过
plot函数绘制出来，但往往我们需要对其中的数据进行进一步的操作。因此，需要对`cfit`类型进行一定的
了解。通过一次简单地试错，我们发现cfit类型的数据有一个子参数p.

<!-- more -->

```
>> fitresult.q
错误使用 cfit/subsref (line 18)
The name 'q' is not a coefficient or a problem parameter.  You can only use dot notation to access the coefficients and problem parameters of a
cfit or sfit, e.g., 'f.p'.
```

```
>> class(fitresult.p)

ans =

struct

>> fitresult.p

ans =

      form: 'pp'
    breaks: [1x84 double]
     coefs: [83x4 double]
    pieces: 83
     order: 4
       dim: 1


```
上述实验表明cfit数据的本质为一个problem，因而其含有参数p. 其p参数是一个`struct`,`struct`
含有`form`等参数，用于指定所采用的拟合函数的类型，以及对应的一些系数等。对于三次样条拟合，我们可
通过函数`ppval`来计算出给定一个自变量`xq`对应的函数值`yq`.
```
xq = linspace(0,100,500);
yq = ppval(fitresult.p, xq);
```
xq 和 yq 即为原数据(x1,y1)进行三次样条拟合后的数据集。

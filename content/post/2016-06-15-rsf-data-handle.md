---
title: 合并 Madagascar 道头与数据
date: 2016-06-15 13:43:37
author: Nick
categories: [地震学软件]
tags: [Madagascar]
---

## Why?

我们都知道 Madagascar 的数据和道头是放置在不同的位置，然后在道头里通过指针指向数据文件的实际位置。这样做的好处有一下几点:

- 误删文件不会造成实际数据的丢失
- 本地移动文件的实际移动的只是道头，这样可以实现快速复制或者剪切整个文件夹
- ...

因此，在大多数时候这种机制都是非常方便我们操作和处理文件及数据。但有些时候我们需要将道头和数据放置在一起，类似于`.su`或`.segy`格式，从而保证数据的完整性。比如，我们在服务器计算叠前偏移，最后得到一个偏移结果，然后需要将结果备份到本地做进一步的分析和处理(画图等),这时候就需要数据是完整的。

<!-- more -->

## How?

对于一个`.rsf`文件，有几种方式可以达到这一目的。Madagascar 的官方说明文档写的是在处理数据的时候直接加一个输出选项就可以实现这一功能。该例子如下:
```{.console}
$ sfspike n1=10 --out=stdout > spike.rsf
$ grep in spike.rsf
Binary file spike.rsf matches

$ sfin spike.rsf
spike.rsf:
    in="stdin"  
    esize=4 type=float form=native
    n1=10          d1=0.004       o1=0          label1="Time" unit1="s"
        10 elements 40 bytes
	```

但如果处理的时候没有使用`--out=stdout`选项，那么如何将道头和数据打包在一起呢？对于上面这个例子只需要重新写一个命令加上`--out=stdout`选项就行了，因为运行时间短，这样最快捷最简便。但是如果对于某些生成最终结果需要很长时间的文件，这种方法显然显得不合时宜。这时候可以 Madagascar 的`sfcp`命令。假设我们通过下面的命令生成了一个`.rsf`文件

	$ sfspike n1=10 >spike.rsf

然后，我们可以通过`sfin`来查看`spike.rsf`文件的道头信息以及数据文件的位置。
```{.console}
$ sfin spike.rsf
in:
    in="/var/tmp/spike.rsf@"   
    esize=4 type=float form=native
    n1=10          d1=0.004       o1=0          label1="Time" unit1="s"
        10 elements 40 bytes
```
从上面的结果可以看出`spike.rsf`的数据文件是`/var/tmp`目录下`spike.rsf@`文件。如果安装了 Seismic Unix 的话，还可以通过`xwigb`命令来绘制这个结果。

	$ xwigb </var/tmp/spike.rsf@ n1=10

下面采用`sfcp`来合并道头和数据，命令如下:

	$ sfcp <spike.rsf --out=stdout >spikenew.rsf

现在我们通过`sfin`来查看`spikenew.rsf`的数据文件位置

```{.console}
$ sfin <spikenew.rsf
in:
    in="stdin"
    esize=4 type=float form=native   
    n1=10          d1=0.004       o1=0          label1="Time" unit1="s"
        10 elements 40 bytes

```
这时候我们发现数据和道头文件合并在一起了(`in="stdin"`).相信大家会有种上当受骗的感觉，想想这不还是用到了官方中说的那个选项`--out=stdout`么？因此，通过这个例子可以发现，几乎任何一个 Madagascar 的命令都可以实现这样的操作，但`sfcp`应该是比较合理的了，因为它只是单纯的复制文件而已，如果要使用一些其他命令的话，可能会出现一些奇怪的事情，大家可以尽情尝试。

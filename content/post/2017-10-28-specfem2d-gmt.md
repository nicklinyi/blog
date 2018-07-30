---
title: 采用 GMT 绘制 SPECFEM2D 波场快照
date: 2017-10-28 12:47:24
author: Nick
categories: [绘图]
tags: [GMT, SPECFEM2D]
---

## 前言
SPECFEM2D 本身程序运行的结果也可以输出波场快照，但是其图形由于是自己生成的，并不美观，也不便于
与其他模拟程序生成的波场快照进行对比。因此本文记录如何采用 GMT 绘制 SPECFEM2D 所生成的波场快照。

<!-- more -->

## 运行程序中的一些参数设置
这里主要显示绘图需要的部分参数：

```
#### for wavefield dumps ####
output_wavefield_dumps          = .true.        #将这里设为 true， 主要是输出绘图文件
imagetype_wavefield_dumps       = 1              # display 1=displ vector 2=veloc vector 3=accel vector 4=pressure， 这儿一般选择绘制位移，也可以选择绘制其他的，根据自己的需要进行修改
use_binary_for_wavefield_dumps  = .false.        # use ASCII or single-precision binary format for the wave field dumps， 由于 GMT 画 ASCII 文件相对简单一点，因此这里选择 false

```

## 运行程序并绘图

运行完程序之后，在`OUTPUT_FILES`文件夹下应该存在很多波场快照的文件(文件名类似于 `wavefield0000100_01.txt`)，最后还有一个网格节点坐标的文件(文件名类似于`wavefield_grid_for_dumps.txt`)。其中波场快照文件共有两列，分别为水平向和垂直向的波场信息。网格节点坐标文件也有两列数据，每一行记录一个网格节点的坐标信息(x z)。波场快照文件和网格节点坐标文件行数都相等，为总共计算的网格点数（程序运行的时候会在终端上打印该节点数）。

### 构建绘图原始文本
由于波场快照文件记录的是每个网格节点上的波场值，因此和传统的有限差分程序输出的波场快照文件不同。属于不规则网格数据（即使是一个均匀分布的网格），因此无法通过 SU, Madagascar 直接绘制波场快照信息。只能通过绘制一个 xyz 数据体的格式来进行，这个采用 GMT 很容易实现。但前提是需要把原来输出的波场快照，网格节点转化为一个文本文件，然后再使用 GMT 来绘图。

构建绘图原始文本可采用 Linux 下的 `paste` 命令，其操作如下
```
# 将 wavefield0001000_01.txt 按列粘贴到 wavefield_grid_for_dumps.txt 中，并存储为 wavefield_ux_uz.txt
$ paste  wavefield_grid_for_dumps.txt wavefield0001000_01.txt > wavefield_ux_uz.txt
```
通过上述处理后， `wavefield_ux_uz.txt`文件的前两列分别为 x,y 坐标；后两列为 ux, uz 值。

### GMT绘图脚本
```
#!/bin/bash

# 模型的尺寸为1000 m * 1000 m
R="-R0/1000/0/1000"
data="wavefield_ux_uz.txt"
grid="output.grd"
J="-JX10c/10c"
PS="wavefield_ux.ps"

# 将前三列数据生成网格文件，用于绘制ux；若要绘制uz,修改为-i0,1,3即可
gmt surface  -i0,1,2 $R $data -I2/2 -G$grid

# 绘制底图
gmt psbasemap  $R  $J -P -Bxa100 -Bya100 -K  > $PS

# 绘制波场快照
gmt grdimage $grid $R $J -I -Cgray -O  >> $PS

# ####
gmt psconvert $PS -Tg -E300 -A -Tf
rm gmt.history
rm *.grd
```

### 结果图
只是一个简单的示例，未添加横坐标和纵坐标的label，大家可自行修改。这里显示的绘制的结果不是ux,也不是uz,
而是`u=sqrt(ux**2 + uz**2)`。

<div align="center">
<img src="http://upload-images.jianshu.io/upload_images/1703880-097c8d13426b0e34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" style="zoom:40%" alt="波场快照图" align=center/>
</div>
注意：
之前波场输出文件的选项`use_binary_for_wavefield_dumps  = .false.`建议选为false，生成文本文件。最开始我选的是true主要的考虑是占用更小的磁盘空间，当时想着用 ximage 来画一个初步的波场快照信息，结果通过分析文件字节数怎么算都有个二倍的数据量，后面才想到是通过记录了两个波场分量的信息。如果采用ascii文本文件输出的话，就不会走这么多弯路了。



---
title: macOS 安装 SeismicJulia 
date: 2018-07-25
tags: ["SeismicJulia"]
author: Lloyd
categories: ["软件"]
slug: seismicjulia
---

## Julia
SeismicJulia 采用 Julia 语言编写，因此要先安装 Julia. macOS 安装 Julia 非常简单，
直接上[官网](https://julialang.org/downloads/)下载 dmg 镜像文件安装即可。

安装完成后，可以配置一下环境变量，在`${HOME}/.bash_profile` 文件末尾添加一行即可：
```
# PATH 后面添加 julia 可执行二进制文件的路径即可
export PATH="/Applications/Julia-0.6.app/Contents/Resources/julia/bin:$PATH"
```

## Seismic.jl
在任一终端下输入：
```
$ julia 
```
即可启动 Julia, 安装 Seismic.jl 只需要下面的命令即可：
```
julia> Pkg.add("Seismic")
julia> Pkg.checkout("Seismic") 
```

## 示例运行
我们以一个简单的二维 FK 滤波为例，具体代码见链接 https://github.com/SeismicJulia/Seismic.jl/blob/master/test/test_SeisFKFilter.jl

首先找到`test_SeisFKFilter.jl`文件的目录，然后在该目录下运行
```
$ julia test_SeisFKFilter.jl
```
如果没有报错的话，应该出现以下四幅图片，表示安装成功。

![Figure_1.png](https://upload-images.jianshu.io/upload_images/1703880-32730aa930139b94.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Figure_2.png](https://upload-images.jianshu.io/upload_images/1703880-04ff347183250297.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Figure_3.png](https://upload-images.jianshu.io/upload_images/1703880-cd031953d958b184.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Figure_4.png](https://upload-images.jianshu.io/upload_images/1703880-4c2834d844a77f7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 参考资料
1. https://github.com/SeismicJulia/Seismic.jl


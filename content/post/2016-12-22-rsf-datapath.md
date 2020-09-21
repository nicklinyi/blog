---
title: DATAPATH 的使用
date: 2016-12-22
author: Lloyd
categories: ["地震学软件"]
tags: ["Madagascar"]
slug: rsf-datapath
---

正常情况下， 执行完Madagascar命令后的二进制数据文件是存在`/var/tmp/`下的，实际我们在写自己就的项目的时候，一般最好是把数据文件和项目存在一起，这样管理起来比较方便。因此需要了解Madagascar存储文件时选择`DATAPATH`的优先级关系。具体见官方说明文档 http://www.ahay.org/wiki/Guide_to_RSF_file_format。

<!-- more -->

简述如下：

- 查看命令行是否含`--out=XXX`参数，如果有，则按其指定的方式进行存储，没有则继续进行下面的操作
- 查看命令行是否含`datapath=XXX`参数，如果有，则按按其指定的路径进行存储，没有则继续进行下面的操作
- 查看当前路径下是否含`.datapath`文件，如果有，则按该文件内指定的路径进行存储，没有则继续进行下面的操作
- 查看当前用户的根目录下是否含`.datapath`文件，如果有，则按该文件内指定的路径进行存储，没有则继续进行下面的操作
- 查看系统的环境变量是否含`DATAPATH`变量，如果有，则按该环境变量指定的路径进行存储，没有则继续进行下面的操作
- 以上都没有的话，会直接将二进制存在当前工作路径下。

因此，为了保证项目的完整型，选择第二种方式进行处理。修改$RSFROOT/share/madagascar/etc/env.sh文件中的`DATAPATH`变量：
```
# Path for binary data files part of RSF datasets
export DATAPATH=./
```
最后在终端运行`source ~/.bashrc`即可。

## 修订历史
- 2016.12.02:  初稿；
- 2020.09.21： 新增修改环境变量模式；

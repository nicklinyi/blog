---
title: Linux下复制单个文件到多个文件夹
date: 2016-12-17 16:45:30
updated: 2016-12-17 16:45:30
author: Nick
categories: [Linux]
tags: [Linux]
---

## 问题
复制一个文件到多个文件夹中

## 解决方案
```
$ echo ~/test1 ~/test2 | xargs -n 1
cp -v test.py
```

上面的命令，目录的路径(`~/test1`,`~/test2`)被管道作为输入到`xargs`命令中，
含义是:
- `-n 1`告诉xargs命令每个命令行最多使用一个参数，并发送到cp命令中;
- `cp`用于复制文件;
- `-v`启用详细模式来显示更多细节

执行的结果如下
```
$ echo ~/test1 ~/test2 | xargs -n 1
cp -v test.py
'test.py' -> '/home/nick/test1/test.py'
'test.py' -> '/home/nick/test2/test.py' 
```



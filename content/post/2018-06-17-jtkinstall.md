---
title: Windows 下 MinesJTK 安装指南
date: 2018-06-17
tags: ["MinesJTK"]
author: Lloyd
categories: ["地震学软件"]
slug: jtkinstall
---

### 下载源码
```
> git clone https://github.com/MinesJTK/jtk.git
```

### 编译
进入`jtk\`目录，在命令提示符中输入
```
> gradlew
```
待操作完成后，若在`core\build\libs\`下发现有`edu-mines-jtk-x.x.x.jar`文件，表示编译成功

### 测试
进入`jtk\`目录，在命令提示符中输入
```
> gradlew test
BUILD SUCCESSFUL in 12m 15s.apache.org/maven2/org/jacoco/org.jacoco.agent/0.8.0/org.jacoco.agent-0.8.0.jar
8 actionable tasks: 5 executed, 3 up-to-date2/org/python/jython-standalone/2.7.0/jython-standalone-2.7.0.pom
```
若最终结果如图中所示，则表示单元测试成功。
### 示例程序
1. 示例一(Java)
```
> gradlew run -P demo=mosaic.PlotFrameDemo
```
其结果为下图所示
![frame](https://i.postimg.cc/htXSp6R6/frame.png)

2. 示例二(Jython)
```
> gradlew run -P demo=mosaic/PlotFrameDemo.py
```
其结果为下图所示
![sine](https://i.postimg.cc/m2XL3RJ7/sine.png)



### 参考
https://github.com/MinesJTK/jtk



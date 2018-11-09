---
title: 优化 Madagascar 程序
date: 2017-08-13
author: Nick
categories: [地震学软件]
tags: [Madagascar]
slug: optmize-rsf-code
---

## 前言
在勘探地球物理中，往往一个程序会重复计算多次，比如叠前数据的合成（需要对一个有限差分程序往复执行上
百次，最后得到一个叠前的炮集记录）。因此学会对程序进行优化是一个很好的解决方案。

GNU C Compiler (GCC) 自带了一个通用的 Profile 工具 `gprof`， 用于对程序各函数的运行时间进行
统计，方便用户确认程序的 Hot Spot(运行耗时最多的函数)。下面将介绍如何将该工具用于 Madagascar
程序的 Profile 中。
<!-- more -->
## 准备工作
首先，准备好要 Profile 的程序，这里为`Mfdtd2d.c`。然后写一个用来编译该程序的 `SConstruct` 脚本，代码如下：

    ```
    import os, sys, re, string


    try:
        import bldutil
        glob_build = True # scons command launched in RSFSRC
        srcroot = '../..' # cwd is RSFSRC/build/user/jeff
        Import('env bindir libdir pkgdir')
        env = env.Clone()
    except:
        glob_build = False # scons command launched in the local directory
        srcroot = os.environ.get('RSFSRC', '../..')
        sys.path.append(os.path.join(srcroot,'framework'))
        import bldutil
        env = bldutil.Debug() # Debugging flags for compilers
        ################## very import !!!!!!!! #########################
        env['CC'] = 'gcc -pg' # add option `-pg`  to gcc to enable gprof
        #################################################################
        bindir = libdir = pkgdir = None


    targets = bldutil.UserSconsTargets()

    # C mains
    targets.c = '''
    fdtd2d
    '''

    targets.build_all(env, glob_build, srcroot, bindir, libdir, pkgdir)

    ```

从编译脚本可以看到 gcc 的编译选项里增加了`-pg`选项，这是采用`gprof`来 Profile 所必须的。
如果自己手写编译脚本的话，记得在编译和链接两步都要加上`-pg`选项，否则无法成功运行`gprof`。

## 编译并链接代码

    $ scons

运行完该语句会在当前目录下生成一个可执行的二进制文件`sffdtd2d`。然后我们正常运行该程序。假设
其所需的参数为：速度模型`vel.rsf`, 子波`wav.rsf`，生成的地震记录为`dd.rsf`。然后运行该程序

    $ ./sffdtd2d < vel.rsf wav=wav.rsf > dd.rsf

## Profile

执行完`./sffdtd2d < vel.rsf wav=wav.rsf > dd.rsf`语句后，我们可以在当前目录下发现一个命名
为`gmon.out`的文件。下一步将使用该文件进行 Profile。

执行`gprof ./sffdtd2d gmon.out -p`，将得到下列信息：

    $ gprof ./sffdtd2d gmon.out -p
    Flat profile:

    Each sample counts as 0.01 seconds.
      %   cumulative   self              self     total           
     time   seconds   seconds    calls  ms/call  ms/call  name    
     96.28     76.63    76.63     1000    76.63    76.63  fd_kernel
      1.99     78.21     1.58                             main
      1.75     79.60     1.39     5000     0.28     0.28  fun()
      0.00     79.60     0.00        4     0.00     0.00  fun2()
      0.00     79.60     0.00        2     0.00     0.00  fun3()
      0.00     79.60     0.00        1     0.00     0.00  fun4()
      0.00     79.60     0.00        1     0.00     0.00  fun5()

通过该信息，我们发现`fd_kernel`这个函数占用了96.28%的计算时间，因此我们找到了程序的 Hot Spot
，从而可以通过采用 OpenMP， MPI, CUDA C等方式进一步对程序进行优化加速。

## 参考资料：
* [Gprof官方文档](https://sourceware.org/binutils/docs/gprof/Executing.html#Executing)
* [SCons官方文档](http://scons.org/doc/production/HTML/scons-user.html#sect-external-environments)
* [使用 GNU profile 来提高代码运行速度](https://www.ibm.com/developerworks/cn/linux/l-gnuprof.html)



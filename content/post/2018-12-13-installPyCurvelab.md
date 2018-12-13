---
title: Linux 安装 PyCurvelab
date: 2018-12-13
tags: ["PyCurvelab","CurveLab","FFTW"]
author: Lloyd
categories: ["安装"]
slug: installPyCurvelab
---

### 前言
[PyCurvelab](https://github.com/slimgroup/PyCurvelab) 是对于著名的曲波变换工具 [CurveLab](http://www.curvelet.org) 采用 Python 进行封装调用的一
个软件包。目前只支持 Python2，感兴趣的朋友可将其升级至 Python3。其软件虽小，但由于其依赖于许多比较老旧的软件包，所以安装起来特别费劲。另外，网上关于如何
成功安装该软件的教程几乎为零，因此才有此博文。

### 依赖软件项
参考官网网站的安装说明和我实际安装的情况，其依赖软件包有：
1. Python
	-> version 2.3 or above
	-> http://www.python.org

2. SWIG
	-> version 1.3 or above
	-> http://www.swig.org

3. Numpy
	-> module must be available in your PYTHONPATH environment variable in order to be found by 
	-> version 1.0 or above
	-> http://numpy.scipy.org/

4. CurveLab
	-> version 2.0.2 or above
	-> http://www.curvelet.org
 
5. FFTW
  -> version 2.1.5
  -> http://www.fftw.org/download.html

官方安装说明里只提到了前四个，但由于 CurveLab 的安装依赖于 FFTW(注意，不是FFTW3)。而 FFTW 与 FFTW3 是不兼容的。鉴于未来的发展趋势，有兴趣的朋友可以将
CurveLab 升级到支持 FFTW3 的新版本。

依赖软件包的前三项都与 Python 相关，最简单的办法就是安装一个Anaconda，基本上就已经装好了上述三个软件包了。下面主要介绍 CurveLab, FFTW, 以及 
PyCurvelab.

### 安装 FFTW 2.1.5
先下载软件包，链接为 http://www.fftw.org/download.html。

```
# 解压该软件
$ tar -xvf fftw-2.1.5.tar.gz 

# 进入FFTW-2.1.5软件根目录
$ cd fftw-2.1.5   

# configure (非常重要！！！)
# CC为C编译器， CFLAGS为C编译器的选项，一定要加上`-fPIC`!!!, --prefix为软件安装路径， LDFLAGS为链接器选项，貌似要加上`-fPIC`，这里不太确定，
# 但如果大家安装失败了，就一定要把LDFLAGS="-fPIC"加上，后面我会解释原因
$ ./configure CC=gcc CFLAGS="-O3 -fomit-frame-pointer -fno-schedule-insns -fschedule-insns2 -malign-double -fstrict-aliasing -fPIC"\
--prefix=/home/lloyd/software/fftw-2.1.5 LDFLAGS="-fPIC"  

# 编译
$ make

# 安装
$ make install
```

至此， FFTW-2.1.5 的安装就此结束。

### 安装 CurveLab 2.1.3
CurveLab 2.1.3 软件只提供给从事于学术研究的人，需要在其网站上注册，链接为 http://www.curvelet.org/register.php，注册之后才可以下载，目前最新的
版本是2.1.3, 貌似是2008年更新的。所以希望有感兴趣的朋友将其改写成支持FFTW3的版本，相信也不是特别难。有精力的同学可以尝试一下。


#### 解压该软件
```
$ tar -xvf CutveLab-2.1.3.tar.gz
```

#### 修改`makefile.opt`文件
`makefile.opt`文件位于CutveLab-2.1.3的根目录下。

```
# 进入CutveLab-2.1.3软件根目录
$ cd CutveLab-2.1.3 

# 打开`makefile.opt`文件
$ gedit makefile.opt
```

打开后，需要修改内容如下：
```
# ----------------------------machine dependent file
MODE = optim

# extern library and program variables
FFTW_DIR = /home/lloyd/software/fftw-2.1.5   # 修改为自己的FFTW-2.1.5的安装路径
MEX 		= 	mex

# compiler and linker variable 
INCLUDES =	-I${FFTW_DIR}/include            # 照此句修改即可

LIBS = -L${FFTW_DIR}/lib -lfftw              # 照此句修改即可
```

由于 PyCurvelab 只需要安装CutveLab的两个组件`fdct_wrapping_cpp`和`fdct3d`，下面分别进行描述其安装过程。

```
# 进入fdct_wrapping_cpp的src文件夹中
$ cd fdct_wrapping_cpp/src
# 编译`libfdct_wrapping.a`
$ make

# 进入fdct3d的src文件夹中
$ cd fdct3d/src
# 编译`libfdct3d.a`
$ make 
```

至此，与 CurveLab-2.1.3 的相关的依赖部分安装就此结束。

### 安装 PyCurvelab
成功安装好上述依赖项之后，安装PyCurvelab就显得比较容易了。但由于其安装脚本需要调用CurveLab和FFTW的安装目录，因此需要设置两个环境变量。
在当前用户的`~/.bashrc`中加入如下语句
```
# CurveLab-2.1.3
export FDCT=/home/lloyd/software/CurveLab-2.1.3

# FFTW-2.1.5
export FFTW=/home/lloyd/software/fftw-2.1.5
```
添加完之后，`$ source ~/.bashrc`更新一下当前终端的环境变量。


```
# 下载PyCurvelab
$ git clone https://github.com/slimgroup/PyCurvelab.git

# 进入到PyCurvelab根目录
$ cd PyCurvelab

# 编译
$ python2 setup build 

# 安装
$ sudo python2 setup install  # 这里采用root权限是因此，python安装在了系统根目录，如果没有的话则用不需要添加
```

### 可能出现的问题
首先，如果严格按照本博文的顺利进行安装的话，应该是不会有任何问题的。但有时候可能您安装的时候并没有看到此博文，可能安装的过程中出现了一些比较奇怪的问题。
最典型的就是这个问题了：
```
/usr/bin/ld: /home/lloyd/software/fftw-2.1.5/lib/libfftw.a(fftwnd.o): relocation 
R_X86_64_PC32 against symbol `stdout@@GLIBC_2.2.5' can not be used when making 
a shared object; recompile with -fPIC
/usr/bin/ld: final link failed: Bad value
collect2: error: ld returned 1 exit status
error: command 'x86_64-linux-gnu-g++' failed with exit status 1
```

这个问题主要是由于`-fPIC`选项在一些地方没有添加所导致的。具体该选项的意义大家可以自己查询一下。我们发现，报错信息已经具体的给出了哪个文件(`fftwnd.o`)
存在问题，因此我们需要查询一下编译`fftwnd.o`文件的时候我们到底采用了`-fPIC`选项没有？我们找到`/home/lloyd/software/fftw-2.1.5/lib/libfftw.a`文件
，然后对其进行解压，再用命令进行分析。其过程如下：
```
# 进入到libfftw.a的安装路径
$ cd /home/lloyd/software/fftw-2.1.5/lib/

# 解压libfftw.a
$ ar -x libfftw.a 

# 查询是否编译时采用了`-fPIC`选项没有
$ readelf --relocs fftwnd.o | egrep '(GOT|PLT|JU?MP_SLOT)'
# 若该语句返回的是空，则没有采用`-fPIC`选项，如果有值，则采用了。
# 下面是采用`-fPIC`选项的显示结果

00000000005a  001d00000004 R_X86_64_PLT32    0000000000000000 fftw_malloc - 4
0000000000b1  001d00000004 R_X86_64_PLT32    0000000000000000 fftw_malloc - 4
0000000000be  001d00000004 R_X86_64_PLT32    0000000000000000 fftw_malloc - 4
0000000000cb  001d00000004 R_X86_64_PLT32    0000000000000000 fftw_malloc - 4
000000000151  001d00000004 R_X86_64_PLT32    0000000000000000 fftw_malloc - 4
0000000001c1  001d00000004 R_X86_64_PLT32    0000000000000000 fftw_malloc - 4
0000000001e2  001f00000004 R_X86_64_PLT32    0000000000000000 memset - 4
00000000028f  002100000004 R_X86_64_PLT32    0000000000000000 fftw_create_plan - 4
000000000315  002200000004 R_X86_64_PLT32    0000000000000000 fftw_destroy_plan - 4
00000000032b  002300000004 R_X86_64_PLT32    0000000000000000 fftw_free - 4
000000000642  002400000004 R_X86_64_PLT32    0000000000000360 fftwnd_work_size - 4
00000000069b  002600000004 R_X86_64_PLT32    0000000000000000 fftw_create_plan_speci - 4
000000000705  002300000004 R_X86_64_PLT32    0000000000000000 fftw_free - 4
00000000076d  002200000004 R_X86_64_PLT32    0000000000000000 fftw_destroy_plan - 4
000000000780  002300000004 R_X86_64_PLT32    0000000000000000 fftw_free - 4
00000000078a  002300000004 R_X86_64_PLT32    0000000000000000 fftw_free - 4
0000000007a7  001d00000004 R_X86_64_PLT32    0000000000000000 fftw_malloc - 4
000000000835  002200000004 R_X86_64_PLT32    0000000000000000 fftw_destroy_plan - 4
000000000846  002300000004 R_X86_64_PLT32    0000000000000000 fftw_free - 4
000000000855  002300000004 R_X86_64_PLT32    0000000000000000 fftw_free - 4
000000000864  002300000004 R_X86_64_PLT32    0000000000000000 fftw_free - 4
000000000873  002300000004 R_X86_64_PLT32    0000000000000000 fftw_free - 4
000000000882  002300000004 R_X86_64_PLT32    0000000000000000 fftw_free - 4
0000000008dd  002900000004 R_X86_64_PLT32    0000000000000000 fwrite - 4
00000000092e  002a00000004 R_X86_64_PLT32    0000000000000000 fprintf - 4
00000000094d  002900000004 R_X86_64_PLT32    0000000000000000 fwrite - 4
00000000096a  002a00000004 R_X86_64_PLT32    0000000000000000 fprintf - 4
0000000009a7  002a00000004 R_X86_64_PLT32    0000000000000000 fprintf - 4
0000000009e6  002b00000004 R_X86_64_PLT32    0000000000000000 fftw_fprint_plan - 4
000000000a1d  002a00000004 R_X86_64_PLT32    0000000000000000 fprintf - 4
000000000a51  002900000004 R_X86_64_PLT32    0000000000000000 fwrite - 4
000000000a63  002d0000002a R_X86_64_REX_GOTP 0000000000000000 stdout - 4
000000000ce3  002f00000004 R_X86_64_PLT32    0000000000000000 fftw - 4
000000000e00  002f00000004 R_X86_64_PLT32    0000000000000000 fftw - 4
000000000ee1  003000000004 R_X86_64_PLT32    0000000000000e30 fftwnd_aux - 4
000000000f2f  002e00000004 R_X86_64_PLT32    0000000000000a80 fftw_buffered - 4
000000000f72  002f00000004 R_X86_64_PLT32    0000000000000000 fftw - 4
000000000fc2  002f00000004 R_X86_64_PLT32    0000000000000000 fftw - 4
00000000100e  002f00000004 R_X86_64_PLT32    0000000000000000 fftw - 4
0000000010f7  003100000004 R_X86_64_PLT32    0000000000001030 fftwnd_aux_howmany - 4
00000000117e  002f00000004 R_X86_64_PLT32    0000000000000000 fftw - 4
000000001224  002e00000004 R_X86_64_PLT32    0000000000000a80 fftw_buffered - 4
0000000012bc  002f00000004 R_X86_64_PLT32    0000000000000000 fftw - 4
000000001356  002f00000004 R_X86_64_PLT32    0000000000000000 fftw - 4
0000000013e7  002f00000004 R_X86_64_PLT32    0000000000000000 fftw - 4
000000001476  003000000004 R_X86_64_PLT32    0000000000000e30 fftwnd_aux - 4
0000000014d7  003100000004 R_X86_64_PLT32    0000000000001030 fftwnd_aux_howmany - 4
000000001517  001d00000004 R_X86_64_PLT32    0000000000000000 fftw_malloc - 4
00000000177a  003400000004 R_X86_64_PLT32    0000000000000000 fftw_gettimeofday_get_ - 4
00000000179f  003400000004 R_X86_64_PLT32    0000000000000000 fftw_gettimeofday_get_ - 4
0000000017cf  003200000004 R_X86_64_PLT32    0000000000001380 fftwnd - 4
0000000017db  003400000004 R_X86_64_PLT32    0000000000000000 fftw_gettimeofday_get_ - 4
0000000017fa  003500000004 R_X86_64_PLT32    0000000000000000 fftw_gettimeofday_time - 4
000000001822  003500000004 R_X86_64_PLT32    0000000000000000 fftw_gettimeofday_time - 4
00000000185e  003500000004 R_X86_64_PLT32    0000000000000000 fftw_gettimeofday_time - 4
000000001886  003500000004 R_X86_64_PLT32    0000000000000000 fftw_gettimeofday_time - 4
000000001992  001b00000004 R_X86_64_PLT32    0000000000000000 fftwnd_create_plan_aux - 4
0000000019d5  002400000004 R_X86_64_PLT32    0000000000000360 fftwnd_work_size - 4
0000000019ef  001e00000004 R_X86_64_PLT32    00000000000001b0 fftwnd_new_plan_array - 4
000000001a02  002000000004 R_X86_64_PLT32    00000000000001f0 fftwnd_create_plans_ge - 4
000000001a17  001e00000004 R_X86_64_PLT32    00000000000001b0 fftwnd_new_plan_array - 4
000000001a3f  002500000004 R_X86_64_PLT32    0000000000000600 fftwnd_create_plans_sp - 4
000000001a7f  002400000004 R_X86_64_PLT32    0000000000000360 fftwnd_work_size - 4
000000001a9b  003300000004 R_X86_64_PLT32    0000000000001540 fftwnd_measure_runtime - 4
000000001ac3  002400000004 R_X86_64_PLT32    0000000000000360 fftwnd_work_size - 4
000000001adf  003300000004 R_X86_64_PLT32    0000000000001540 fftwnd_measure_runtime - 4
000000001b0e  002300000004 R_X86_64_PLT32    0000000000000000 fftw_free - 4
000000001b7f  002200000004 R_X86_64_PLT32    0000000000000000 fftw_destroy_plan - 4
000000001b9a  002300000004 R_X86_64_PLT32    0000000000000000 fftw_free - 4
000000001bad  002400000004 R_X86_64_PLT32    0000000000000360 fftwnd_work_size - 4
000000001be9  001d00000004 R_X86_64_PLT32    0000000000000000 fftw_malloc - 4
000000001c00  002700000004 R_X86_64_PLT32    00000000000007c0 fftwnd_destroy_plan - 4
000000001c09  001e00000004 R_X86_64_PLT32    00000000000001b0 fftwnd_new_plan_array - 4
000000001c1c  002000000004 R_X86_64_PLT32    00000000000001f0 fftwnd_create_plans_ge - 4
000000001c52  001d00000004 R_X86_64_PLT32    0000000000000000 fftw_malloc - 4
000000001cbd  002200000004 R_X86_64_PLT32    0000000000000000 fftw_destroy_plan - 4
000000001cce  002300000004 R_X86_64_PLT32    0000000000000000 fftw_free - 4
000000001d32  002200000004 R_X86_64_PLT32    0000000000000000 fftw_destroy_plan - 4
000000001d4d  002300000004 R_X86_64_PLT32    0000000000000000 fftw_free - 4
000000001dba  002200000004 R_X86_64_PLT32    0000000000000000 fftw_destroy_plan - 4
000000001dd0  002300000004 R_X86_64_PLT32    0000000000000000 fftw_free - 4
000000001e00  003600000004 R_X86_64_PLT32    0000000000001970 fftwnd_create_plan_spe - 4
000000001e22  003700000004 R_X86_64_PLT32    0000000000001de0 fftw2d_create_plan_spe - 4
000000001e63  003600000004 R_X86_64_PLT32    0000000000001970 fftwnd_create_plan_spe - 4
000000001e7e  003900000004 R_X86_64_PLT32    0000000000001e30 fftw3d_create_plan_spe - 4
000000001ea2  003600000004 R_X86_64_PLT32    0000000000001970 fftwnd_create_plan_spe - 4
000000001ecf  003200000004 R_X86_64_PLT32    0000000000001380 fftwnd - 4
000000000892  002300000004 R_X86_64_PLT32    0000000000000000 fftw_free - 4
000000000a38  002900000004 R_X86_64_PLT32    0000000000000000 fwrite - 4
000000000a6e  002800000004 R_X86_64_PLT32    00000000000008a0 fftwnd_fprint_plan - 4
0000000014fa  002300000004 R_X86_64_PLT32    0000000000000000 fftw_free - 4
```

### 参考资料
- [PyCurveLab官方安装指南](https://github.com/slimgroup/PyCurvelab)
- [FFTW官方安装指南](http://www.fftw.org/fftw3_doc/Installation-on-Unix.html#Installation-on-Unix)
- [错误分析](https://stackoverflow.com/questions/19768267/relocation-r-x86-64-32s-against-linking-error)

---
title: Madagascar Matlab 混合编程
date: 2018-05-20 08:57:57
tags: ['Matlab', 'Madagascar']
author: Lloyd
categories: ['编程']
---

## 配置
安装含Matlab API的Madagascar过程详见[Madagascar安装](http://nickwhyy.top/awesome-installation/posts/Madagascar.html)

## 写`.rsf`文件
下面的matlab程序提供了一个使用`linear_events`来合成一个水平地震事件的例子 
```matlab
function Linear(out)

% linear_events.m : downloaded from http://seismic-lab.physics.ualberta.ca/
d1 = linear_events(0.004,40,2,[0:10:10*79],1,0,1,2,2,2013);

% create header
rsf_create(out,size(d1)');

% write data
rsf_write(d1,out,'same');
```
在`SConstruct`中的调用如下：
```python
from rsf.proj import*
from rsf.prog import RSFROOT

########################################################################
# INITIALIZATION
########################################################################
matlab         = WhereIs('matlab')
matROOT = './'
matfun = 'Linear'
matlabpath = os.environ.get('MATLABPATH',os.path.join(RSFROOT,'lib'))

if not matlab:
    sys.stderr.write('\nCannot find Matlab.\n')
    sys.exit(1)

Flow('d',[os.path.join(matROOT,matfun+'.m')],
     '''MATLABPATH=%(matlabpath)s %(matlab)s
     -nosplash -nojvm -r
     "addpath %(matROOT)s;%(matfun)s('${TARGET}');quit"
     '''%vars(),stdin=0,stdout=-1)

End()
```
运行上述脚本`SConstruct`后生成的数据如下图所示

![d.png](https://upload-images.jianshu.io/upload_images/1703880-e3e4b4625022f8a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 读`.rsf`文件
```matlab
function LinearWrite(in,out)

% read in size, delta of input header
% size: [n1 n2 n3 ...]
% delta: [d1 d2 d3 ...]
[sz, delta] = rsf_read_header(in);

% allocate space
d1 = zeros(sz);

% read in input data
rsf_read(d1,in,'same');

% generate another linear event
d2 = linear_events(0.004,40,2,[0:10:10*79],0.5,0.001,1,2,2,2013);

d = d1 + d2;

% create header
rsf_create(out,in);

% write data
rsf_write(d,out,'same');
```
对应的`SConstruct`如下
```python
from rsf.proj import*
from rsf.prog import RSFROOT

########################################################################
# INITIALIZATION
########################################################################
matlab         = WhereIs('matlab')
matROOT = './'
matfun = 'LinearWrite'
matlabpath = os.environ.get('MATLABPATH',os.path.join(RSFROOT,'lib'))

if not matlab:
    sys.stderr.write('\nCannot find Matlab.\n')
    sys.exit(1)

Flow('d2',[os.path.join(matROOT,matfun+'.m'),'d'],
     '''MATLABPATH=%(matlabpath)s %(matlab)s
     -nosplash -nojvm -r
     "addpath %(matROOT)s;%(matfun)s('${SOURCES[1]}','${TARGET}');quit"
     '''%vars(),stdin=0,stdout=-1)

Plot('d2','grey wanttitle=n')

End()
```

生成的数据如下图所示

![d2.png](https://upload-images.jianshu.io/upload_images/1703880-3dbfd5b92f0d9aa6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 函数参数处理
```matlab
% LinearPar函数多了一些数值型的参数dt,f0,tmax
function LinearPar(in,out,dt,f0,tmax)

% read in size, delta of input header
% size: [n1 n2 n3 ...]
% delta: [d1 d2 d3 ...]
[sz, delta] = rsf_read_header(in);

% allocate space
d1 = zeros(sz);

% read in input data
rsf_read(d1,in,'same');

% generate another linear event
d2 = linear_events(dt,f0,tmax,[0:10:10*79],0.5,0.001,1,2,2,2013);

d = d1 + d2;

% create header
rsf_create(out,in);

% write data
rsf_write(d,out,'same');
```
对应的`SConstruct`脚本如下
```python
from rsf.proj import*
from rsf.prog import RSFROOT

########################################################################
# INITIALIZATION
########################################################################
matlab         = WhereIs('matlab')
matROOT = './'
matfun = 'LinearPar'
matlabpath = os.environ.get('MATLABPATH',os.path.join(RSFROOT,'lib'))

if not matlab:
    sys.stderr.write('\nCannot find Matlab.\n')
    sys.exit(1)

dt = 0.004
f0 = 40
tmax = 2

Flow('d3',[os.path.join(matROOT,matfun+'.m'),'d'],
     '''MATLABPATH=%(matlabpath)s %(matlab)s
     -nosplash -nojvm -r
     "addpath %(matROOT)s;%(matfun)s('${SOURCES[1]}','${TARGET}',%(dt)g,%(f0)g,%(tmax)g);quit"
     '''%vars(),stdin=0,stdout=-1)

End()
```

## 参考
https://github.com/ahay/src/blob/master/book/tccs/emdpf/




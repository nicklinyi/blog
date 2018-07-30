---
title: 合成地震事件
date: 2018-04-24 13:08:28
author: Lloyd
categories: [地震学]
tags: [合成数据]
---

## 前言
地震事件的合成主要用于测试一些信号处理的方法，比如插值、去噪等。为了避免大家重复造轮子，这里介绍一下合成地震事件的方法。

## 线性地震事件合成步骤
### 前期准备
这里采用SAIG组开发的SeismicLab中公开的代码`linear_events.m`来进行线性地震数据的合成。
由于该代码中默认是要添加随机噪音的，有时候我们并不需要添加随机噪音的数据，因此，我对源程序进行了修改，将添加噪音的部分删除了，[点击这里查看具体的代码内容](https://github.com/nicklinyi/seismic_utils/tree/master/synthetics/linear_events.m)。

<!-- more -->

### 开始试验
```
clear;
close all;

dt = 0.004;  % 时间间隔
dx = 20;     % 道间距
nt = 512;              % 总时间采样个数
tmax = (nt-1)*dt;      % 记录最大时刻
f0 = 25;               % 地震子波的主频
nx = 128;              % 空间采样个数
h = [0:dx:dx*(nx-1)];  % 偏移距

tau = [0.4, 0.7, 1.8]; % 共三个地震事件，其值为事件与时间轴的交点的时刻
p = [1.6/max(h), 0.5/max(h), -1.3/max(h)]; % 三个地震事件的射线参数
amp = [1, 1, 1];          % 三个地震事件的振幅

% 合成三个地震事件，d
[d,h,t] = linear_events(dt,f0,tmax,h,tau,p,amp);

%%
figure;
wigb(d,1,h,t); % 用SeismicLab中的`wigb.m`函数绘制波形图
set(gca,'TickDir','out')
ax = gca;
ax.XAxisLocation = 'top';
xlabel('Distance (m)');
ylabel('Time (s)');
```

运行完上述代码后，得到的图形如下
![线性地震事件](https://upload-images.jianshu.io/upload_images/1703880-d5011298fb447a0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 双曲地震事件
### 前期准备
这里采用SAIG组开发的SeismicLab中公开的代码`hyperbolic_events.m`来进行线性地震数据的合成。
由于该代码中默认是要添加随机噪音的，有时候我们并不需要添加随机噪音的数据，因此，我对源程序进行了修改，将添加噪音的部分删除了，[点击这里查看具体的代码内容](https://github.com/nicklinyi/seismic_utils/tree/master/synthetics/hyperbolic_events.m)。
### 开始试验
```
clear;
close all;

%%
dt = 0.002; % 时间间隔
dx = 5;     % 道间距
nt = 350;   % 总时间采样个数
tmax = (nt-1)*dt;     % 记录最大时刻
f0 = 30;              % 地震子波的主频
nx = 80;              % 空间采样个数
h = [0:dx:dx*(nx-1)]; % 偏移距

tau = [0.15, 0.28, 0.39]; % 共三个地震事件，其值为事件与时间轴的交点的时刻
v = [1500, 1750, 1800];   % 三个地震事件的速度
amp = [1, 1, 1];          % 三个地震事件的振幅

% 合成三个地震事件，d
[d,h,t] = hyperbolic_events(dt,f0,tmax,h,tau,v,amp);

figure;
wigb(d,1,1:length(h),t);
set(gca,'TickDir','out')
ax = gca;
ax.XAxisLocation = 'top';
xlabel('Trace');
ylabel('Time (s)');

```

运行完上述代码后，得到的图形如下
![双曲地震事件](https://upload-images.jianshu.io/upload_images/1703880-588726ecd9fd9e68.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



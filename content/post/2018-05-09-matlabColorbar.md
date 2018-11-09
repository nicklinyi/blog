---
title: Matlab如何绘制美观的colorbar
date: 2018-05-09
tags: [Matlab, colorbar]
categories: [绘图]
---

通常情况下，matlab绘出的色标主要存在两个问题：1色标的宽度太宽；2色标的高度太高。为了绘制更美观的图像，这里深入研究了一下色标的大小调整方法。

1.如何修改色标的大小，以及控制色标在图片中的位置？
```
fig=figure;
fig.Units='inch';
fig.Position=[4 4 3.5 4];
imagesc(linspace(0,1,201),linspace(0,250,600),Mask), axis ij
set(gca,'xaxislocation','top','Fontsize',10,...
    'TickDir','out')
ax=gca;
ax.Position = [0.2 0.1 0.65 0.75] %控制坐标轴的位置，以及长宽比
c = colorbar;
c.Position = [0.88 0.22 0.025 0.5]; %控制色标的位置以及长宽比
colormap(jet);
xlabel('Normalized Wavenumber','FontSize',10),ylabel('Frequency (Hz)','FontSize',10);
% set(gcf,'PaperPosition',[0 0 10 12]);
print -r300 -dtiff spectra.tif
```
<!-- more -->
![demo.png](https://upload-images.jianshu.io/upload_images/1703880-6dd04234b930e631.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




2.如何控制色标的标度，这样绘制多幅图片的时候能保证是同一个色标。
查询色标的范围可以单独使用
```
cc = caxis; % 查询当前图片的色标范围并保存为cc变量
```
在绘图语句中加入下面这句即可。
```
caxis(cc); %将上述存储的cc变量作为这副图片的色标范围，达到两幅图色标一致的目的。
```

3.很多时候我们需要进行多图展示，但只有其中的几幅图需要colorbar。如果直接用matlab来绘制多图的话，很难控制每幅图的坐标轴长度都一致。同理，绘制单幅图也很难保证带色标图的坐标轴的长度与不带色标图的坐标长度一致。

对于这种情况，我的建议是绘制单幅图，优势有很多。单幅图可反复多次利用，也用来拼接成多幅，也可以单幅单独使用，十分便利。

在保证单幅图的情况下，我们需要保证每幅图的大小是一致的，这个通过 1 中给出的控制图的大小的语句即可完成。接下来需要保证是带色标的图的色标保持一致，这个通过 2 中给出的例子即可完成。接下来就是绘图的时候不要加色标，每幅图都单独成图，色标一致即可，但不加色标。最后，我们再单独生成一个色标范围与各图保持一致的色标即可。可采用如下命令
```
fig=figure;
fig.Units='inch';
fig.Position = [4 4 1.0 4];
axis off
colormap(jet) % 色标颜色方案与前面图保持一致，很重要！！！
c = colorbar;
c.Position = [0.53 0.28 0.10 0.5] % 控制色标的位置以及长宽比
caxis(cc) % 与前面保持一致，保证色标值与前面相吻合，也和数据相吻合。
```

![colorbar.png](https://upload-images.jianshu.io/upload_images/1703880-6f386db7c2581b4d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

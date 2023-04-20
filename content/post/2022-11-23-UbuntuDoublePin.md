---
title: Ubuntu 22.04 LTS 下安装 小鹤双拼
date: 2022-11-17

tags: [Linux，输入法]
categories: ['安装']
slug: ubuntudoublepin
---

## 前言

双拼输入法能大幅提高打字的效率，这里主要介绍如何在Ubuntu 22.04 LTS上安装双拼输入法

## 安装



```bash
$ sudo apt install ibus
$ sudo apt install ibus-libpinyin
$ sudo apt install ibus-rime
$ sudo apt install librime-data-double-pinyin
```
安装好上述软件后，在`~/.config/ibus`下新建文件夹`rime`
```
$ mkdir ~/.config/ibus/rime
```
然后，新建两个文件`~/.config/ibus/rime/default.custom.yaml`和`~/.config/ibus/rime/ibus_rime.custom.yaml`。
其中`~/.config/ibus/rime/default.custom.yaml`的内容如下（下面代码中`#`后面的为注释部分）

```
patch:
    schema_list:
        - schema: double_pinyin_flypy # 小鹤双拼  
    menu: 
        page_size: 9 # 设置后选词的个数为9个
```
这段代码用于配置小鹤双拼方案，并设置候选词为9个。

`~/.config/ibus/rime/ibus_rime.custom.yaml`文件内容如下：
```
patch:
    style:
        horizontal: true
```
这段代码的目的是设置输入法候选词的显示方式为横向。

## 配置
设置好以后内容后，还需要在`~/.bashrc`中添加如下内容：
```
   export GTK_IM_MODULE=ibus
   export XMODIFIERS=@im=ibus
   export QT_IM_MODULE=ibus
   export LC_CTYPE=zh_CN.UTF-8
   ibus-daemon -d -x
```
然后需要设置如下内容，打开`Settings->Keyboard->Input Sources`添加`Chinese->Chinese(Rime)`.

然后登出系统，再重新登入系统。

## 繁体与简体切换
在激活小鹤双拼输入法的情况下，使用`Ctrl+~`进行繁体鹤简体的切换




## 修订历史

- 2022-11-23：初稿；
- 2023-04-20: 新增繁体与简体切换；

## 参考资料
- https://www.likecs.com/show-477129.html#sc=2647
- https://www.cnblogs.com/willwuss/p/14354143.html
- https://www.bilibili.com/read/cv14690644

---
title: Vim 高亮 SConstruct
date: 2016-05-24
tags: ["SConstruct",  "Vim"]
categories: ["编程"]
slug: vim-scons-highlight
---

Vim高亮文件一般只对含后缀的文件名有效，对于经常使用 Madagascar 的人来说，每次采用 Vim 写 SConstruct 文件显得十分不便，因此如果能让 Vim 自动识别 SConstruct 文件为 Python 文件，则处理起来会很方便.

## 方法一
对于 SCons 要编译的文件，不命名为 SConstruct, 而命名为其他以`.py`结尾的文件，然后用 SCons 编译的时候指定该文件名。虽然可行，但还是没有解决根本问题。

## 方法二
每次打开 SConstruct 文件后进入 Vim 命令行模式将文件类型设置为 Python 。
```
:set ft=python
```

## 方法三
在`~/.vimrc`文件中指定所有的 SCons 相关的文件类型为 python，可直接在`~/.vimrc`文件末尾添加下列语句即可达成目标
```
" Filetype related
au BufNewFile,BufRead SConstruct,sconstruct,SConscript,sconscript set ft=python
```

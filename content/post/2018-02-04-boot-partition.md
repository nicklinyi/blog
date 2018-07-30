---
title: Linux /boot 重新分区
date: 2018-02-04 08:57:57
author: Lloyd
tags: [Linux]
categories: [编程]
---



由于之前装机时 `/boot` 只分配了 200MB 的空间，而 Ubuntu 16.04 LTS 会自动更新内核， 导致某次开机突然提示 `/boot` 空间不足， 然后发现 `/boot`下有两个系统内核文件，一个 44M 左右， 再加上一些其他的文件，加起来足足占了 70% 的使用空间，后期如果再更新系统，将会十分不便。因此想到了扩大 `/boot` 空间。

网上的一些博文都是简单的介绍了如何增大`/boot`, 没有提供一个详细的步骤，这里总结了各大博文的精华之处，整理出来供大家参考。

<!-- more -->

-    运行`sudo cat /etc/fstab`
```{console}
➜  ~ sudo cat /etc/fstab
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda7 during installation
UUID=50b02ca0-b7d6-47e7-b822-9d30caac57ec /               ext4    errors=remount-ro 0       1
# /boot was on /dev/sda6 during installation
UUID=c1a85b1e-b617-4f2f-82d4-0ed61e8347ba /boot           ext4    defaults        0       2
# swap was on /dev/sda5 during installation
UUID=3db90030-2801-4ba3-b126-7bb0af433172 none            swap    sw              0       0
```
-  运行`sudo ls -al /dev/disk/by-uuid/*`
```
➜  ~ sudo ls -al /dev/disk/by-uuid/*
lrwxrwxrwx 1 root root 10 8月  13 10:28 /dev/disk/by-uuid/041838F41838E678 -> ../../sda4
lrwxrwxrwx 1 root root 10 8月  13 10:28 /dev/disk/by-uuid/3db90030-2801-4ba3-b126-7bb0af433172 -> ../../sda5
lrwxrwxrwx 1 root root 10 8月  13 10:27 /dev/disk/by-uuid/40D69E0DD69E0378 -> ../../sdb1
lrwxrwxrwx 1 root root 10 8月  13 10:27 /dev/disk/by-uuid/50b02ca0-b7d6-47e7-b822-9d30caac57ec -> ../../sda7
lrwxrwxrwx 1 root root 10 8月  13 10:27 /dev/disk/by-uuid/6C7AC53E7AC505B4 -> ../../sdc1
lrwxrwxrwx 1 root root 10 8月  13 10:28 /dev/disk/by-uuid/7AC85EFBC85EB4DB -> ../../sda2
lrwxrwxrwx 1 root root 10 8月  13 10:27 /dev/disk/by-uuid/8646ADF646ADE6E1 -> ../../sdb2
lrwxrwxrwx 1 root root 10 8月  13 10:28 /dev/disk/by-uuid/948E13708E134A5E -> ../../sda1
lrwxrwxrwx 1 root root 10 8月  13 10:27 /dev/disk/by-uuid/a6686297-cdf6-4600-9465-dcf2de344662 -> ../../sda8
lrwxrwxrwx 1 root root 10 8月  13 10:28 /dev/disk/by-uuid/c1a85b1e-b617-4f2f-82d4-0ed61e8347ba -> ../../sda6
```

通过这个命令将自己的 `/boot` 与 硬盘编号`sd*` 以及 uuid 值对应起来。

-  制作一个live 启动盘， 用它来启动系统。
然后在 live 系统中运行`gparted`。
```
$ sudo gparted
```
这时会出现一个图形界面。修改分区信息在图形界面很简单，我只说明一下我的分区策略：

由于我的 `swap` 分区与 `/boot` 挨着， 而且 `swap` 分区空间也挺大，大概分了15GB。 这里采用的方式为直接将 `swap` 进行 `resize`, 然后将后面大概1gb的空间留出来。 接着对`/boot` 进行 `resize` 然后再刚才留出来的1gb 加进来即可。最后, 点击上面的绿色的√即可完成重新分区的过程。

-  确认 `/boot`等的 uuid 值是否改变
在 live 系统中看 `/boot` 的 uuid 值可通过 `gparted` 右键查看，然后与你之前在原来系统上运行的结果比较，看是否一致。
***使用`gparted` 右键查看的时候，不要选择 New uuid ，不然你得修改`/etc/fstab` 文件，将其对应上。***
如果在原来系统上运行的结果没有保存，可在 live 里访问原来系统的文件， 然后再查看`/etc/fstab` 文件。

- 重启系统。

#### 参考资料：
- <http://forum.ubuntu.org.cn/viewtopic.php?t=481011>
- <http://blog.csdn.net/caz28/article/details/50827904>

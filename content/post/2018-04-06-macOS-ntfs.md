---
title: macOS 挂载 NTFS 格式硬盘
date: 2018-04-06 08:57:57
tags: [macOS, NTFS]
categories: [macOS]
---

Mac本身是支持NTFS写入的，只是NTFS是微软开发，由于版权和技术细节原因，苹果不愿公开说自己支持NTFS写入，也是有自己以后可能不支持NTFS写入的考量。

<!-- more -->
方法一：
1.在terminal下使用命令`diskutil list`查询要挂载的NTFS硬盘名称
```
$ diskutil list
UUID=7E84540E-D729-44B9-9AB8-2056C250DC5D none auto ro
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *251.0 GB   disk0
   1:                        EFI EFI                     209.7 MB   disk0s1
   2:                 Apple_APFS Container disk1         250.8 GB   disk0s2

/dev/disk1 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +250.8 GB   disk1
                                 Physical Store disk0s2
   1:                APFS Volume Macintosh HD            76.4 GB    disk1s1
   2:                APFS Volume Preboot                 19.5 MB    disk1s2
   3:                APFS Volume Recovery                509.8 MB   disk1s3
   4:                APFS Volume VM                      3.2 GB     disk1s4

/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *4.0 TB     disk2
   1:                        EFI EFI                     209.7 MB   disk2s1
   2:                  Apple_HFS macOSbackup             1000.0 GB  disk2s2
   3:       Microsoft Basic Data data                    3.0 TB     disk2s3
```
从输出的结果可以看到，最下面那个`data`即为我的名称。

2.在terminal下使用命令`diskutil info /Volumes/data`查询该硬盘的UUID值
```
$ diskutil info /Volumes/data
Device Identifier:        disk2s3
Device Node:              /dev/disk2s3
Whole:                    No
Part of Whole:            disk2

Volume Name:              data
Mounted:                  Yes
Mount Point:              /Volumes/data

Partition Type:           Microsoft Basic Data
File System Personality:  NTFS
Type (Bundle):            ntfs
Name (User Visible):      Windows NT File System (NTFS)

OS Can Be Installed:      No
Media Type:               Generic
Protocol:                 USB
SMART Status:             Not Supported
Volume UUID:              C0F838DA-F857-9CC0-2CC1-9CE6F19CE668
Disk / Partition UUID:    2E13FC4C-6FC4-469F-B8DE-FBBAA5F5907D

Disk Size:                3.0 TB (3000273383424 Bytes) (exactly 5859908952 512-Byte-Units)
Device Block Size:        512 Bytes

Volume Total Space:       3.0 TB (3000273379328 Bytes) (exactly 5859908944 512-Byte-Units)
Volume Used Space:        16.1 GB (16052121600 Bytes) (exactly 31351800 512-Byte-Units) (0.5%)
Volume Available Space:   3.0 TB (2984221257728 Bytes) (exactly 5828557144 512-Byte-Units) (99.5%)
Allocation Block Size:    4096 Bytes

Read-Only Media:          No
Read-Only Volume:         Yes

Device Location:          External
Removable Media:          Fixed
```
输出结果中`Volume UUID`即为`data`的UUID值。

3.在`/etc/fstab`中添加如下一行
```
UUID=C0F838DA-F857-9CC0-2CC1-9CE6F19CE668 none ntfs rw,auto,nobrowse
```
具体操作如下：
```
$ sudo vim /etc/fstab
```
然后输入刚才那一行（UUID值根据自己电脑的情况更改），最后保存退出（`wq`）.

4.退出自己的移动硬盘，然后拔出移动硬盘，再重新插上移动硬盘。这时，在桌面上不再显示移动硬盘的图标，但我们打开`terminal`，进入`/Volumes`文件夹就能看到了。
```
$ cd /Volumes
$ ls
Macintosh HD	data		macOSbackup
```
5.通过软链接建立桌面快捷方式
```
$ mkdir -p ~/Desktop/data
$ ln -s /Volumes/data ~/Desktop/data
```
在桌面上，我们就能看到其快捷方式了。

方法二.
https://blog.plcent.com/archives/209
目前暂未测试该方法，所以没有把详细过程记录下来。

##补充说明
方法一对于最新的系统macOS High Sierra是有效的。


##参考资料
https://techsviewer.com/write-ntfs-driver-macos-high-sierra/

https://blog.plcent.com/archives/209

[aijiv提供的答案](https://www.zhihu.com/question/19571334)

---
layout: post
title:  "Linux 磁盘挂载"
categories: Linux 
tags:  Linux 
---

* content
{:toc}

本文讲解Linux系统下如何挂载磁盘。

## 查看系统磁盘

首先使用命令`fdisk -l`查看系统磁盘情况：

```
    Disk /dev/vda: 107.4 GB, 107374182400 bytes
    255 heads, 63 sectors/track, 13054 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x0003d038
    
       Device Boot      Start         End      Blocks   Id  System
    /dev/vda1   *           1       13055   104855552   83  Linux
    
    Disk /dev/vdb: 107.4 GB, 107374182400 bytes
    16 heads, 63 sectors/track, 208050 cylinders
    Units = cylinders of 1008 * 512 = 516096 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x53564f44
    
       Device Boot      Start         End      Blocks   Id  System
    /dev/vdb1               1      208050   104857168+  83  Linux
```

显示信息说明系统共有两块磁盘，分别是`/dev/vda1`、`/dev/vdb1`

## 查看磁盘挂载情况

使用命令`df -h`查看系统磁盘挂载情况：

```
    Filesystem      Size  Used Avail Use% Mounted on
    /dev/vda1        99G  4.4G   90G   5% /
    tmpfs           499M     0  499M   0% /dev/shm
    /dev/vdb1        99G   12G   82G  13% /alidata

```

显示信息说明磁盘`/dev/vda1`挂载目录为系统目录根目录`/`，即为系统盘，`/dev/vdb1`挂载目录为`/alidata`为数据盘。

## 磁盘挂载

使用命令`mount /dev/vadb1 /alidata`，是将数据盘`/dev/vdb1`挂载到`/alidata`目录下。

参考地址：[https://help.aliyun.com/document_detail/25426.html?spm=5176.11065259.1996646101.searchclickresult.26a41cef792ya8](https://help.aliyun.com/document_detail/25426.html?spm=5176.11065259.1996646101.searchclickresult.26a41cef792ya8)
---
title: Linux高级系统管理（网易云课堂）
date: 2023-08-10 21:39:33
updated:
tags: LINUX
categories: LINUX
keywords: 
description:
top_img:
comments:
cover:
toc:
toc_number:
copyright:
copyright_author:
copyright_author_href:
copyright_url:
copyright_info:
mathjax:
katex:
aplayer:
highlight_shrink:
aside: 
---

# Linux高级系统管理（网易云课堂）

## 第一章 磁盘管理：LVM逻辑卷

### 课时1 LVM逻辑卷基本概念

传统磁盘管理的问题：当分区大小不够用时无法扩展其大小，只能通过添加硬盘、创建新的分区来扩充空间，但是新添加进来的硬盘是作为独立文件系统存在的，原有的文件系统并未的到扩充，上层应用很多时候只能访问一个文件系统。只能让现有磁盘下线，换上新的磁盘之后，再将原始数据导入。

LVM（Logical volume Manager）逻辑卷管理通过将底层物理硬盘抽象封装起来，以逻辑卷的形式表现给上层系统，逻辑卷的大小可以动态调整，而且不会丢失现有数据。新加入的硬盘也不会改变现有上层的逻辑卷。作为一种动态磁盘管理机制，逻辑卷技术大大提高了磁盘管理的灵活性。

+ PE (physical Extend)
+ PV (physical volume)
+ VG (volume group)
+ LV (logical volume)

（1）物理磁盘被格式化为PV，空间被分为一个个PE。

（2）不同的PV加入同一个VG，不同PV的PE全部进入VG的PE池内。

（3）LV基于PE创建，大小为PE的整数倍，组成LV的PE可能来自不同物理磁盘。

（4）LV现在就直接可以格式化后挂载使用了。

（5）LV的扩充缩减实际上就是增加或减少组成该LV的PE的数量。其过程不丢失原始数据。

当我们创建好卷组VG之后，在`/dev/vgname/lvname`就会多出了一个以卷组名命名的文件夹。创建好LV之后，会在VG文件夹下出现LV文件（实际上是链接）。

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202308102229679.png)

### 课时2 LVM逻辑卷的基本管理操作

创建LVM

1．将物理磁盘设备初始化为物理卷：`pvcreate /dev/sdb /dev/sdc`

2．创建卷组，并将PV加入卷组中：`vgcreate liniuxcast/dev/sdb/dev/sdc`

3．基于卷组创建逻辑卷：`lvcreate -n mylv-L 2G linuxcast`

4．为创建好的逻辑卷创建文件系统：`mkfs.ext4 /dev/linuxcast/mylv`

5．将格式化好的逻辑卷挂载使用：`mount /dev/linuxcast/mylv /mnt`

查看LVM

可以通过以下命令查看LVM相关信息：

1．查看物理卷信息：`pvdisplay`（详细） `pvs`

2．查看卷组信息： `vgdisplay`（详细） `vgs`

3．查看逻辑卷信息： `lvdisplay`（详细） `lvs`

删除LVM

1．删除LV：`lvremove /dev/linuxcast/mylv`

2．删除VG：`vgremove linuxcast`

3．删除物理卷：`pvremove /dev/sdb`

### 课时3 LVM逻辑卷的拉伸与缩小

拉伸一个逻辑卷。

逻辑卷的拉伸可以在线执行，不需要卸载逻辑卷。

1．保证VG中有足够的空闲空间：`vgdisplay` 

2．扩充逻辑卷：`Ivextend -L +1G /dev/linuxcast/mylv`

3．查看扩充后LV大小：`lvdisplay`

4．更新文件系统：`resize2fs /dev/linuxcast/mnylv`

5．查看更新后文件系统：`df -h`

拉伸一个卷组。			

1．将要添加到VG的硬盘格式化为PV：`pvcreate/dev/sdd`

2．将新的PV添加到指定卷组中：`vgextend linuxcast/dev/sdd`

3．查看扩充后VG大小：`lvgdisplay`

缩小一个逻辑卷。

逻辑卷的缩小不可以在线执行，需要卸载逻辑卷。

1．卸载已经挂载的逻辑卷：`umount /dev/linuxcast/mylv`

2．缩小文件系统（会提示需要运行的ck检查文件系统）：`resize2fs /dev/linuxcast/mylv 1G` 

3．缩小LV：`Ivreduce-L-IG/dev/linuxcast/mylv` 

4．查看缩小后的LV：`Ivdisplay` 

5．挂载：`mount /dev/linuxcast/mylv /mnt` 

缩小一个卷组。

1．将一个PV从制定卷组中移除：`vgreduce linuxcast/dev/sdd`

2．查看缩小后的卷组大小：`vgdisplay`

## 第二章 Linux高级权限 - ACL

### 课时4 Linux高级权限 - ACL

传统权限模型缺点：传统的UGO权限模型无法应对复杂的权限设置需求，如对于一个文件只能设置一个组，并且对改组进行权限控制，但是如果该文件有多个组会对其进行访问，并且都要进行权限限制时，传统的UGO模型就无法满足需求了。

ACL（Access Control List）是一种高级权限机制，允许我们对一个文件 或文件夹进行灵活的、复杂的权限设置。

根分区默认已经打开ACL，如果我们自己挂载的分区要使用ACL，需要在挂载文件的时候打开ACL功能：`mount -o acl /dev/sda5 /mnt`

ACL允许针对不同用户、不同组对一个目标文件或文件夹进行权限设置，不受UGO模型限制。

ACL命令

+ 查看一个文件／文件夹的ACL设置：`getfacl linuxcast.net`
+ 针对一个用户对文件进行ACL设置：`setfacl -m u:nash_su:rwx linuxcast.net`
+ 针对一个组对文件进行ACL设置：`setfacl -m g:training:rw linuxcast.net`
+ 删除一个ACL设置：`setfacl -x u:nash_su linuxcast.net `

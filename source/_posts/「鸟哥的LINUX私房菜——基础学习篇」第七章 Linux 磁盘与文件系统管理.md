---
title: 「鸟哥的LINUX私房菜——基础学习篇」第七章 Linux 磁盘与文件系统管理
date: 2023-08-10 14:49:33
updated:
tags: LINUX
categories: LINUX
keywords: 
description:
top_img:
comments:
cover:
top: 100
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

# 第七章 Linux 磁盘与文件系统管理

## 7.1 认识 Linux 文件系统

### 7.1.1 磁盘组成与分区的复习

+ 早期的分区主要以柱面为最小分区单位，现在的分区通常使用扇区为最小分区单位（每 个扇区都有其号码喔，就好像座位一样）。
+ MBR 分区表中，第一个扇区最重要，里面有：主要开机区（Master boot record, MBR）及分区表（partition table）， 其中 MBR 占有 446 Bytes，而 partition table 则占 有 64 Bytes。
+ GPT 分区表除了分区数量扩充较多之外，支持的磁盘容量也可以超过 2TB。

### 7.1.2 文件系统特性

+ 传统的磁盘与文件系统之应用中，一个分区就是只能够被格式化成为一个文件系统，所以我们可以说一个 filesystem 就是一个 partition。但是由于新技术的利用，例如我们常听到的 LVM 与软件磁盘阵列（software raid）， 这些技术可以将一个分区格式化为多个文件系统（例如 LVM），也能够将多个分区合成一个文件系统（LVM, RAID）！ 所以说，目前我们在格式化时已经不再说成针对 partition 来格式化了， 通常我们可以称呼一个可被挂载的数据为一个文件系统而不是一个分区喔！

+ superblock：记录此 filesystem 的整体信息，包括inode/block的总量、使用量、剩余量， 以及文件系统的格式与相关信息等；

+ inode：记录文件的属性，一个文件占用一个inode，同时记录此文件的数据所在的 block 号码；

+ block：实际记录文件的内容，若文件太大时，会占用多个 block 。

  ![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed//202308111403046.png)

+ 这种数据存取的方法我们称为索引式文件系统（indexed allocation）。那有没有其他的惯用文件系统可以比较一下啊？ 有的，那就是我们惯用的U盘（闪存），U盘使用的文件系统一般为 FAT 格式。FAT 这种格式的文件系统并没有 inode 存在，所以 FAT 没有办法将这个文件的所有 block 在一开始就读取出来。每个 block 号码都记录在前一个 block 当中， 他的读取方式有点像下面这样。

  ![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed//202308111403444.png)

### 7.1.3 Linux 的 EXT2 文件系统（inode）

+ 如果仔细考虑一下，如果我的文件系统高达数百GB时， 那么将所有的 inode 与 block 通通放置在一起将是很不智的决定，因为 inode 与 block 的数量太庞大，不容易管理。 为此之故，因此 Ext2 文件系统在格式化的时候基本上是区分为多个区块群组（block group）的，每个区块群组都有独立的 inode/block/superblock 系统。

  ![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed//202308111404435.jpg)

+ 在整体的规划当中，文件系统最前面有一个开机扇区（boot sector），这个开机扇区可以安装开机管理程序， 这是个非常重要的设计，因为如此一来我们就能够将不同的开机管理程序安装到个别的文件系统最前端，而不用覆盖整颗磁盘唯一的 MBR， 这样也才能够制作出多重开机的环境啊！
+ data block 是用来放置文件内容数据地方，在 Ext2 文件系统中所支持的 block 大小有 1K, 2K 及 4K 三种而已。不过要注意的是，由于 block 大小的差异，会导致该文件系统能够支持的最大磁盘容量与最大单一文件大小并不相同。
+ 每个 inode 大小均固定为 128 Bytes （新的 ext4 与 xfs 可设置到 256 Bytes）； 每个文件都仅会占用一个 inode 而已； 因此文件系统能够创建的文件数量与 inode 的数量有关。

+ inode 要记录的数据非常多，但偏偏又只有 128Bytes 而已， 而 inode 记录一个 block 号码要花掉 4Byte ！ inode 哪有这么多可记录的信息？为此我们的系统很聪明的将 inode 记录 block 号码的区域定义为12个直接，一个间接, 一个双间接与一个三间接记录区。

  ![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed//202308111404611.jpg)

+ xt4 文件系统的 inode 容量已经可以扩大到 256Bytes 了，更大的 inode 容量，可以纪录更多的文件系统信息，包括新的 ACL 以及 SELinux 类型等， 当然，可以纪录的单一文件大小达 16TB 且单一文件系统总容量可达 1EB 哩！

+ Superblock （超级区块）：记录的信息包含一个 valid bit 数值，若此文件系统已被挂载，则 valid bit 为 0 ，若未被挂载，则 valid bit 为 1 。

+ 事实上除了第一个 block group 内会含有 superblock 之外，后续的 block group 不一定含有 superblock ， 而若含有 superblock 则该 superblock 主要是做为第一个 block group 内 superblock 的备份咯，这样可以进行 superblock 的救援呢！

+ Filesystem Description （文件系统描述说明）：这个区段可以描述每个 block group 的开始与结束的 block 号码，以及说明每个区段 （superblock, bitmap, inodemap, data block） 分别介于哪一个 block 号码之间。这部份也能 够用 dumpe2fs 来观察的。
+ block bitmap （区块对照表） ：增加或删除文件时记录对应 block 的使用情况。

+ inode bitmap （inode 对照表）：记录 inode 的使用情况。

+ dumpe2fs：查询 Ext 家族 superblock 信息的指令。

+ blkid：这个指令可以叫出目前系统有被格式化的设备。

### 7.1.4 与目录树的关系

+ 当我们在 Linux 下的文件系统创建一个目录时，文件系统会分配一个 inode 与至少一块 block 给该目录。其中，inode 记录该目录的相关权限与属性，并可记录分配到的那块 block 号码；而 block 则是记录在这个目录下的文件名与该文件名占用的 inode 号码数据。

+ 如果想要实际观察文件夹内的文件所占用的 inode 号码时，可以使用 ls -i 这个选项来处理。

+ 在目录下面的文件数如果太多而导致一个 block 无法容纳的下所有的文件名与 inode 对照表时，Linux 会给予该目录多一个 block 来继续记录相关的数据。

+ inode 本身并不记录文件名，文件名的记录 是在目录的 block 当中。 因此在第五章文件与目录的权限说明中， 我们才会提到“新增/删除/更名文件名与目录的 w 权限有关”的特色！那么因为文件名是记录在目录的 block 当中， 因此当我们要读取某个文件时，就务必会经过目录的 inode 与 block ，然后才能够找到那个待读取 文件的 inode 号码， 最终才会读到正确的文件的 block 内的数据。

### 7.1.5 EXT2/EXT3/EXT4 文件的存取与日志式文件系统的功能

+ 我们将 inode table 与 data block 称为数据存放区域，至于其他例如 superblock、 block bitmap 与 inode bitmap 等区段就被称为 metadata （中介数据） 啰，因为 superblock, inode bitmap 及 block bitmap 的数据是经常变动的，每次新增、移除、编辑时都可能会影响 到这三个部分的数据，因此才被称为中介数据的啦。

+ 在早期的 Ext2 文件系统中，如果因为写入时断电等原因导致 metadata 的内容与实际数据存放区产生不一致， 那么系统在重新开机的时候，就会借由 Superblock 当中记录的 valid bit （是否有挂载） 与 filesystem state （clean 与否） 等状态来判断是否强制进行数据一致性的检查！若有需要检查时则以 e2fsck 这支程序来进行的。 不过，这样的检查真的是很费时～因为要针对 metadata 区域与实际数据存放区来进行比对， 要搜寻整个 filesystem 呢～如果你的文件系统有 100GB 以上，而且里面的文件数量又多时～真是麻烦～这也就造成后来所谓日志式文件系统的兴起了。
+ 日志式文件系统 （Journaling filesystem）：在 filesystem 当中规划出一个区块，该区块专门在记录写入或修订文件时的步骤。这样万一数据的纪录过程当中发生了问题，那么我们的系统只要去检查日志记录区块， 就可以知道哪个文件发生了问题，针对该问题来做一致性的检查即可，而不必针对整块 filesystem 去检查。

### 7.1.6 Linux 文件系统的运行

+ 我们的 Linux 使用的方式是通过一个称为非同步处理 （asynchronously） 的方式。 当系统载入一个文件到内存后，如果该文件没有被更动过，则在内存区段的文件数据会被设置为干净（clean）的。 但如果内存中的文件数据被更改过了，此时该内存中的数据会被设置为脏的 （Dirty）。此时所有的动作都还在内存中执行，并没有写入到磁盘中！ 系统会不定时的将内存中设置为“Dirty”的数据写回磁盘，以保持磁盘与内存数据的一致性。 你也可以利用第四章谈到的 sync指令来手动强迫写入磁盘。
+ 若正常关机时，关机指令会主动调用 sync 来将内存的数据回写入磁盘内； 但若不正常关机（如跳电、死机或其他不明原因），由于数据尚未回写到磁盘内， 因此重新开机后可能会花很多时间在进行磁盘检验，甚至可能导致文件系统的损毁（非磁盘损毁）。

### 7.1.7 挂载点的意义 （mount point）

### 7.1.8 其他 Linux 支持的文件系统与 VFS

+ 虽然 Linux 的标准文件系统是 ext2 ，且还有增加了日志功能的 ext3/ext4 ，事实上，Linux 还有支持很多文件系统格式的， 尤其是最近这几年推出了好几种速度很快的日志式文件系统， 包括 SGI 的 XFS 文件系统， 可以适用更小型文件的 Reiserfs 文件系统，以及 Windows 的 FAT 文件系统等等， 都能够被 Linux 所支持喔！
+ 想要知道你的 Linux 支持的文件系统有哪些，可以察看下面这个目录：`ls -l  /lib/modules/$(uname -r)/kernel/fs`
+ 系统目前已载入到内存中支持的文件系统则有：`cat /proc/filesystems`
+ Linux VFS （Virtual Filesystem Switch）：整个 Linux 的系统都是通过一个名为 Virtual Filesystem Switch 的核 心功能去读取 filesystem 的。 也就是说，整个 Linux 认识的 filesystem 其实都是 VFS 在进行 管理，我们使用者并不需要知道每个 partition 上头的 filesystem 是什么～ VFS 会主动的帮我 们做好读取的动作呢～

### 7.1.9 XFS 文件系统简介

+ EXT 家族当前较伤脑筋的地方：支持度最广，但格式化超慢！ Ext 文件系统家族对于文件格式化的处理方面，采用的是预先规划出所有的 inode/block/meta data 等数据，未来系统可以直接取用， 不需要再进行动态配置的作法。
+ xfs 文件系统在数据的分布上，主要规划为三个部份，一个数据区 （data section）、一个文件系统活动登录区 （log section）以及一个实时运行区 （realtime section）。
+ 数据区 （data section）：xfs 的这个数据区的储存区群组 （allocation groups, AG），你就将它想成是 ext 家族的 block 群组 （block groups） 就对了！ 每个储存区群组都包含了 （1）整个文件系统的 superblock（2）剩余空间的管理机制（3）inode的分配与追踪。 此外，inode与 block 都是系统需要用到时， 这才动态配置产生，所以格式化动作超级快！与 ext 家族不同的是， xfs 的 block 与 inode 有多种不同的容量可供设置，block 容量 可由 512Bytes ~ 64K 调配，不过，Linux 的环境下， 由于内存控制的关系 （分页档 pagesize 的容量之故），因此最高可以使用的 block 大小为 4K 而已！ 至于 inode 容量可由 256Bytes 到 2M 这么大！不过，大概还是保留 256Bytes 的默认值就很够用了！
+ 文件系统活动登录区 （log section）：其实有点像是日志区啦！文件的变化会在这里纪录下来，直到该变化完整的写入到数据区后， 该笔纪录才会被终结。如果文件系统因为某些缘故 （而损毁时，系统会拿这个登录区块来进行检验，借以快速的修复文件系统。xfs 设计有点有趣，在这个区域中， 你可以指定外部的磁盘来作为 xfs 文件系统的日志区块喔！例如，你可以将 SSD 磁盘作为 xfs 的登录区，这样当系统需要进行任何活动时， 就可以更快速的进行工作！
+ 实时运行区 （realtime section）：当有文件要被创建时，xfs 会在这个区段里面找一个到数个的 extent 区块，将文件放置在这个区块内，等到分配完毕后，再写入到 data section 的 inode 与 block 去！ 这个 extent 区块的大小得要在格式化的时候就先指定，最小值是 4K 最大可到 1G。一般非磁盘阵列的磁盘默认为 64K 容量，而具有类似磁盘阵列的 stripe 情况下，则建议 extent 设置为与 stripe 一样大较佳。这个 extent 最好不要乱动，因为可能会影响到实体磁盘的性能喔。
+ XFS 文件系统的描述数据观察：`xfs_info`

## 7.2 文件系统的简单操作

### 7.2.1 磁盘与目录的容量

+ `df`：列出文件系统的整体磁盘使用量；
+ 另外需要注意的是，如果使用 -a 这个参数时，系统会出现 /proc 这个挂载点，但是里面的东西都是 0 ，不要紧张！ /proc 的东西都是 Linux 系统所需要载入的系统数据，而且是挂载在“内存当中”的， 所以当然没有占任何的磁盘空间啰！

+ 至于那个 /dev/shm/ 目录，其实是利用内存虚拟出来的磁盘空间，通常是总实体内存的一半！ 由于是通过内存仿真出来的磁盘，因此你在这个目录下面创建任何数据文件时，存取速度是非常快速的！（在内存内工作） 不过，也由于他是内存仿真出来的，因此这个文件系统的大小在每部主机上都不一样，而且创建的东西在下次开机时就消失了！ 因为是在内存中嘛！
+ `du`：评估文件系统的磁盘使用量（常用在推估目录所占容量）。

### 7.2.2 实体链接与符号链接： ln

+ 在 Linux 下面的链接文件有两种，一种是类似 Windows 的捷径功能的文件，可以让你快速的链接到目标文件（或目录）； 另一种则是通过文件系统的 inode 链接来产生新文件名，而不是产生新文件！这种称为实体链接 （hard link）。
+ `ls -l`列举的链接数为：有多少个文件名链接到 这个 inode 号码的意思。

+ hard link 是有限制的：不能跨 Filesystem； 不能 link 目录。

+ Symbolic Link （符号链接，亦即是捷径）：Symbolic link 就是在创建一个独立的文件，而这个文件会让数据的读取指向他 link 的那个文件的文件名！由于只是利用文件来做为指向的动作， 所以，当来源文件被删除之后，symbolic link 的文件会“开不了”， 会 一直说“无法打开某文件！”。实际上就是找不到原始“文件名”而已啦！

+ 由于 Hard Link 的限制太多了，包括无法做“目录”的 link ， 所以在用途上面是比较受限的！反而是 Symbolic Link 的使用方面较广喔！
+ `ln [-sf] 来源文件 目标文件`
+ 当我们创建一个新的目录时， “新的目录的 link 数为 2 ，而上层目录的 link 数则会增加 1 ”。

## 7.3 磁盘的分区、格式化、检验与挂载

### 7.3.1 观察磁盘分区状态

+ `lsblk` 可以看成“ list block device ”的缩写，就是列出所有储存设备的意思！
+ `blkid` 列出设备的 UUID 等参数。
+ 什么是 UUID 呢？UUID 是全域单一识别码 （universally unique identifier），Linux 会将系统内所有的设备都给予一个独一无二的识别码， 这个识别码就可以拿来作为挂载或者是使用这个设备/文件系统之用了。
+ `parted device_name print` 列出磁盘的分区表类型与分区信息。

### 7.3.2 磁盘分区： gdisk/fdisk

+ 你应该要通过 lsblk 或 blkid 先找到磁盘，再用 `parted /dev/xxx print` 来找出内部的分区表类 型，之后才用 gdisk 或 fdisk 来操作系统。

+ 

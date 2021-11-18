---
title: 「鸟哥LINUX基础学习篇」第5章 Linux的文件权限与目录配置「待整理 ING ...」
date: 2021-11-18 00:44:33
updated:
tags: Linux
categories:
- [Linux]
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

「鸟哥LINUX基础学习篇」第5章 Linux的文件权限与目录配置「待整理 ING ...」

---

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111180004244.png)

第二栏表示有多少文件名链接到此节点。

每个文件都会将它的权限与属性记录到文件系统的inode中，不过，我们使用的目录树却是用文件名来记录，因此每个文件名就会链接到一个inode，这个属性记录的就是有多少个不同的文件名链接到同一个inode号码

第五栏为文件的容量大小，默认单位为Bytes

---

`ls --help`

`info ls`

---

`chgrp [-R] 用户组 文件/目录`

`chown [-R] 帐号 文件/目录`

`chown [-R] 帐号:用户组 文件/目录`

`chown [-R] 帐号.用户组 文件/目录`

`chown [-R] .用户组 文件/目录`

`chmod [-R] xyz 文件/目录`

> $0 \leq x y z \leq 7$ 
>
> $r=4$ $w=2$ $x=1$

`chmod ugoa +-= rwx 文件/目录, ugoa +-= rwx 文件/目录 ...`

---

复制操作`cp`会复制执行者的属性与权限

---

`w`对于文件：可以改动文件内容，但不能删除文件本身

---

`r`对于目录：表示具有读取目录结构清单的权限，所以当你具有读取（r）一个目录的权限时，表示你可以查询该目录下的文件名数据。 所以你就可以利用 ls 这个指令将该目录的内容列表显示出来！

`w`对于目录：这个可写入的权限对目录来说，是很了不起的！ 因为他表示你具有异动该目录结构清单 的权限，也就是下面这些权限

+ 创建新的文件与目录； 

+ 删除已经存在的文件与目录（不论该文件的权限为何！） 

+ 将已存在的文件或目录进行更名； 

+ 搬移该目录内的文件、目录位置。 总之，目录的w权限就与该目录下面的文件名异动有关就对了啦！

`x`对于目录：目录的x代表的是使用者能否进入该目录成为工作目录的用途！ 所谓的工作目录（work directory）就是你目前所在的目录啦！

---

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111180028054.png)

---

因为是r乍看之下好像就具有可以进入此目录的权限， 其实那是错的。 能不能进入某一个目录，只与该目录的x权限有关啦！此外，工作目录对于 指令的执行是非常重要的，如果你在某目录下不具有x的权限， 那么你就无法切换到该目录下，也就无法执行该目录下的任何指令，即使你具有该目录的r或w的权限。

要开放目录给任何人浏览时，应该至少也要给予r及x的权限，但w权限不可随便给！

---

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111180035285.png)

只有r确实可以让使用者读取目录的文件名列表，不过详细的信息却还是读不到的， 同时也不能将该目录变成工作目录（用 cd 进入该目录之意）。

---

`/dir1/file1`

`/dir2`

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111180036986.png)

修改一个文件至少得有`r`

很多时候 `/dir1` 都不必有 r 耶！为啥？我们知道 `/dir1` 是个目录，也是个抽屉！那个抽屉的 r 代表“这个抽屉里面有灯光”， 所以你能看到的抽屉内的所有数据夹名称 （非内容）。但你已经知道里面的数据夹放在哪个地方，那，有没有灯光有差嘛？ 你还是可以摸黑拿到该数据夹的！对吧！ 因此，上面很多动作中，你只要具有 x 即可！r 是非必备的！只是，没有 r 的话，使用 [tab] 时，他就无法自动帮你补齐文件名了！这样理解乎？

---

「待整理 ING ...」










































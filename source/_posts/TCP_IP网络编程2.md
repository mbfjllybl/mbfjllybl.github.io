---
title: 《TCP/IP网络编程》学习笔记[2]
date: 2023-12-4 10:17:21
tags: UC
categories: UC
---

# 《TCP/IP网络编程》学习笔记[2]

## 第 12 章 I/O 复用

### 12.2 理解 select 函数并实现服务端

使用 select 函数时可以将多个文件描述符集中到一起统一监视，项目如下： 

+ 是否存在套接字接收数据？ 

+ 无需阻塞传输数据的套接字有哪些？ 

+ 哪些套接字发生了异常？

select 函数的调用过程如下图所示：

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed//202312041736751.png)

**设置文件描述符**

利用 select 函数可以同时监视多个文件描述符。当然，监视文件描述符可以视为监视套接字。此时首先需要将要监视的文件描述符集中在一起。集中时也要按照监视项（接收、传输、异常）进行区分，即按照上述 3 种监视项分成 3 类。

+ FD_ZERO(fd_set *fdset) ：将 fd_set 变量所指的位全部初始化成0 

+ FD_SET(int fd,fd_set *fdset) ：在参数 fdset 指向的变量中注册文件描述符 fd 的信息 

+ FD_CLR(int fd,fd_set *fdset) ：从参数 fdset 指向的变量中清除文件描述符 fd 的信息 

+ FD_ISSET(int fd,fd_set *fdset) ：若参数 fdset 指向的变量中包含文件描述符 fd 的信息， 则返回「真」

上述函数中，FD_ISSET 用于验证 select 函数的调用结果，通过下图解释这些函数的功能：

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed//202312041741038.png)

**设置检查（监视）范围及超时**

```c
#include <sys/select.h>
#include <sys/time.h>
int select(int maxfd, fd_set *readset, fd_set *writeset,
fd_set *exceptset, const struct timeval *timeout);
/*
    成功时返回大于 0 的值，失败时返回 -1
    maxfd: 监视对象文件描述符数量
    readset: 将所有关注「是否存在待读取数据」的文件描述符注册到 fd_set 型变量，并传递其地址
    值。
    writeset: 将所有关注「是否可传输无阻塞数据」的文件描述符注册到 fd_set 型变量，并传递其
    地址值。
    exceptset: 将所有关注「是否发生异常」的文件描述符注册到 fd_set 型变量，并传递其地址值。
    timeout: 调用 select 函数后，为防止陷入无限阻塞的状态，传递超时(time-out)信息
    返回值: 发生错误时返回 -1,超时时返回0。因发生关注的时间返回时，返回大于0的值，该值是发生
    事件的文件描述符数。
*/
```














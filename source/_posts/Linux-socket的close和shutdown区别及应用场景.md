---
title: Linux-socket的close和shutdown区别及应用场景
date: 2023-12-01 16:17:21
tags: UC
categories: UC
---

# Linux-socket的close和shutdown区别及应用场景

## **shutdown的定义**

```
#include<sys/socket.h>
int shutdown(int sockfd,int how);
```

how的方式有三种分别是：

+ SHUT_RD（0）：关闭sockfd上的读功能，此选项将不允许sockfd进行读操作。<font color=Red>即该套接字不再接受数据，任何当前在套接字接受缓冲区的数据将被丢弃。</font>进程将不能对该套接字发出任何读操作。对TCP套接字该调用之后接受到的任何数据将被确认然后无声的丢弃掉。
+ SHUT_WR（1）：关闭sockfd的写功能，此选项将不允许sockfd进行写操作，即进程不能在对此套接字发出写操作。
+ SHUT_RDWR（2）：关闭sockfd的读写功能，相当于调用shutdown两次：首先是以SHUT_RD,然后以SHUT_WR。

成功则返回0，错误返回-1，错误码errno：

+ `EBADF`表示sockfd不是一个有效描述符

+ `ENOTCONN`表示sockfd未连接

+ `ENOTSOCK`表示sockfd是一个文件描述符而不是socket描述符

<font color=Red>shutdown()的效果是累计的，不可逆转的。既如果关闭了一个方向数据传输，那么这个方向将会被关闭直至完全被关闭或删除，而不能重新被打开。如果第一次调用了shutdown(0)，第二次调用了shutdown(1)，那么这时的效果就相当于shutdown(2)，也就是双向关闭socket。</font>

## **close的定义**

```
#include<unistd.h>
int close(int fd);
```

关闭读写。成功则返回0，错误返回-1，错误码errno：

+ `EBADF`表示fd不是一个有效描述符
+ `EINTR`表示close函数被信号中断
+ `EIO`表示一个IO错误。

## **二者的区别**

close——<font color=Red>关闭本进程的socket id，但链接还是开着的，用这个socket id的其它进程还能用这个链接，能读或写这个socket id。</font>
shutdown——<font color=Red>破坏了socket 链接</font>，读的时候可能侦探到EOF结束符，写的时候可能会收到一个SIGPIPE信号，这个信号可能直到socket buffer被填充了才收到，shutdown有一个关闭方式的参数，0 不能再读，1不能再写，2 读写都不能。

## socket 多进程中的 shutdown、close 的使用 

<font color=Red>**当所有的数据操作结束以后，你可以调用close()函数来释放该socket，从而停止在该socket上的任何数据操作：close(sockfd);使用close中止一个连接，但它只是减少描述符的参考数，并不直接关闭连接，只有当描述符的参考数为0时才关闭连接。**所以在多进程/线程程序中，close只是确保了对于某个特定的进程或线程来说，该连接是关闭的。使用 client_fd = accept() 后 fork() 以在子进程中处理请求，此时在父进程中使用 close() 关闭该连接，子进程仍可以继续使用该连接。</font>

也可以调用shutdown()函数来关闭该socket。该函数允许你只停止在某个方向上的数据传输，而一个方向上的数据传输继续进行。如你可以关闭某socket的写操作而允许继续在该socket上接受数据，直至读入所有数据。int shutdown(int sockfd,int how);shutdown可直接关闭描述符，不考虑描述符的参考数，可选择中止一个方向的连接。

## **注意**
如果有多个进程共享一个套接字，close每被调用一次，计数减1，直到计数为0时，也就是所用进程都调用了close，套接字将被释放。

<font color=Red>在多进程中如果一个进程中shutdown(sfd, SHUT_RDWR)后其它的进程将无法进行通信。如果一个进程close(sfd)将不会影响到其它进程，得自己理解引用计数的用法了。</font>

 

## **更多关于close和shutdown的说明**

1. <font color=Red>**只要TCP栈的读缓冲里还有未读取（read）数据，则调用close时会直接向对端发送RST**。</font>
2. <font color=Red>**shutdown与socket描述符没有关系，即使调用shutdown(fd, SHUT_RDWR)也不会关闭fd，最终还需close(fd)**</font>。
3. 可以认为shutdown(fd, SHUT_RD)是空操作，因为shutdown后还可以继续从该socket读取数据，这点也许还需要进一步证实。在已发送FIN包后write该socket描述符会引发EPIPE/SIGPIPE。
4. <font color=Red>当有多个socket描述符指向同一socket对象时，调用close时首先会递减该对象的引用计数，计数为0时才会发送FIN包结束TCP连接。shutdown不同，只要以SHUT_WR/SHUT_RDWR方式调用即发送FIN包。</font>
5. SO_LINGER与close，当SO_LINGER选项开启但超时值为0时，调用close直接发送RST（这样可以避免进入TIME_WAIT状态，但破坏了TCP协议的正常工作方式），SO_LINGER对shutdown无影响。
6. TCP连接上出现RST与随后可能的TIME_WAIT状态没有直接关系，主动发FIN包方必然会进入TIME_WAIT状态，**除非不发送FIN而直接以发送RST结束连接**。

## **应用场景** 

通常来说，socket是双向的，即数据是双向通信的。但有些时候，你会想在socket上实现单向的socket，即数据往一个方向传输。单向的socket便称为半开放Socket。要实现半开放式，需要用到shutdown()函数。

一般来说，半开放socket适用于以下场合:

1. 当你想要确保所有写好的数据已经发送成功时。如果在发送数据的过程中，网络意外断开或者出现异常，系统不一定会返回异常，这是你可能以为对端已经接收到数据了。这时需要用shutdown()来确定数据是否发送成功，因为调用shutdown()时只有在缓存中的数据全部发送成功后才会返回。
2. 想用一种方法来捕获程序潜在的错误，这错误可能是因为往一个不能写的socket上写数据，也有可能是在一个不该读操作的socket上读数据。当程序尝试这样做时，将会捕获到一个异常，捕获异常对于程序排错来说是相对简单和省劲的。
3. 当您的程序使用了fork()或者使用多线程时，你想防止其他线程或进程访问到该资源，又或者你想立刻关闭这个socket，那么可以用shutdown()来实现。
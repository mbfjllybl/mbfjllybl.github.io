---
title: 《TCP/IP网络编程》学习笔记
date: 2023-11-29 10:17:21
tags: UC
categories: UC
---

# 《TCP/IP网络编程》学习笔记

## 第 1 章 理解网络编程和套接字

### 1.1 理解网络编程和套接字

**服务端**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>
void error_handling(char *message);

int main(int argc, char *argv[])
{
    int serv_sock;
    int clnt_sock;

    struct sockaddr_in serv_addr;
    struct sockaddr_in clnt_addr;
    socklen_t clnt_addr_size;

    char message[] = "Hello World!";

    if (argc != 2)
    {
        printf("Usage : %s <port>\n", argv[0]);
        exit(1);
    }
    //调用 socket 函数创建套接字
    serv_sock = socket(PF_INET, SOCK_STREAM, 0);
    if (serv_sock == -1)
        error_handling("socket() error");

    memset(&serv_addr, 0, sizeof(serv_addr));
    serv_addr.sin_family = AF_INET;
    serv_addr.sin_addr.s_addr = htonl(INADDR_ANY);
    serv_addr.sin_port = htons(atoi(argv[1]));
    //调用 bind 函数分配ip地址和端口号
    if (bind(serv_sock, (struct sockaddr *)&serv_addr, sizeof(serv_addr)) == -1)
        error_handling("bind() error");
    //调用 listen 函数将套接字转为可接受连接状态
    if (listen(serv_sock, 5) == -1)
        error_handling("listen() error");

    clnt_addr_size = sizeof(clnt_addr);
    //调用 accept 函数受理连接请求。如果在没有连接请求的情况下调用该函数，则不会返回，直到有连接请求为止
    clnt_sock = accept(serv_sock, (struct sockaddr *)&clnt_addr, &clnt_addr_size);
    if (clnt_sock == -1)
        error_handling("accept() error");
    //稍后要将介绍的 write 函数用于传输数据，若程序经过 accept 这一行执行到本行，则说明已经有了连接请求
    write(clnt_sock, message, sizeof(message));
    close(clnt_sock);
    close(serv_sock);
    return 0;
}

void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

**客户端**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>
void error_handling(char *message);

int main(int argc, char *argv[])
{
    int sock;
    struct sockaddr_in serv_addr;
    char message[30];
    int str_len;

    if (argc != 3)
    {
        printf("Usage : %s <IP> <port>\n", argv[0]);
        exit(1);
    }
    //创建套接字，此时套接字并不马上分为服务端和客户端。如果紧接着调用 bind,listen 函数，将成为服务器套接字
    //如果调用 connect 函数，将成为客户端套接字
    sock = socket(PF_INET, SOCK_STREAM, 0);
    if (sock == -1)
        error_handling("socket() error");

    memset(&serv_addr, 0, sizeof(serv_addr));
    serv_addr.sin_family = AF_INET;
    serv_addr.sin_addr.s_addr = inet_addr(argv[1]);
    serv_addr.sin_port = htons(atoi(argv[2]));
    //调用 connect 函数向服务器发送连接请求
    if (connect(sock, (struct sockaddr *)&serv_addr, sizeof(serv_addr)) == -1)
        error_handling("connect() error!");

    str_len = read(sock, message, sizeof(message) - 1);
    if (str_len == -1)
        error_handling("read() error!");

    printf("Message from server : %s \n", message);
    close(sock);
    return 0;
}

void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

### 1.2 基于 Linux 的文件操作

**写文件**

```c
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <unistd.h>
void error_handling(char *message);

int main()
{
    int fd;
    char buf[] = "Let's go!\n";
    // O_CREAT | O_WRONLY | O_TRUNC 是文件打开模式，将创建新文件，并且只能写。如存在 data.txt 文件，则清空文件中的全部数据。
    fd = open("data.txt", O_CREAT | O_WRONLY | O_TRUNC);
    if (fd == -1)
        error_handling("open() error!");
    printf("file descriptor: %d \n", fd);
    // 向对应 fd 中保存的文件描述符的文件传输 buf 中保存的数据。
    if (write(fd, buf, sizeof(buf)) == -1)
        error_handling("write() error!");
    close(fd);
    return 0;
}

void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

**读文件**

```c
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <unistd.h>
#define BUF_SIZE 100
void error_handling(char *message);

int main()
{
    int fd;
    char buf[BUF_SIZE];

    fd = open("data.txt", O_RDONLY);
    if (fd == -1)
        error_handling("open() error!");
    printf("file descriptor: %d \n", fd);

    if (read(fd, buf, sizeof(buf)) == -1)
        error_handling("read() error!");
    printf("file data: %s", buf);
    close(fd);
    return 0;
}
void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

### 1.5 习题

***底层 I/O 函数与 ANSI 标准定义的文件 I/O 函数有何区别？***

答：文件 I/O 又称为低级磁盘 I/O，遵循 POSIX 相关标准。任何兼容 POSIX 标准的操作系统上都支持文件I/O。标准 I/O 被称为高级磁盘 I/O，遵循 ANSI C 相关标准。只要开发环境中有标准 I/O 库，标准 I/O 就可以使用。（Linux 中使用的是 GLIBC，它是标准C库的超集。不仅包含 ANSI C 中定义的函数，还包括 POSIX 标准中定义的函数。因此，Linux 下既可以使用标准 I/O，也可以使用文件 I/O）。

## 第 2 章 套接字类型与协议设置

### 2.1 套接字协议及数据传输特性

`int socket(int domain, int type, int protocol);`

**协议族**

> 头文件 sys/socket.h 中声明的协议族

| 名称      | 协议族               |
| --------- | -------------------- |
| PF_INET   | IPV4 互联网协议族    |
| PF_INET6  | IPV6 互联网协议族    |
| PF_LOCAL  | 本地通信 Unix 协议族 |
| PF_PACKET | 底层套接字的协议族   |
| PF_IPX    | IPX Novel 协议族     |

**套接字类型**

+ 面向连接的套接字（SOCK_STREAM）

+ 面向消息的套接字（SOCK_DGRAM）

**协议最终选择**

第三个参数存在的原因：对同一协议族中可能存在的多个数据传输方式相同的协议，所以数据传输方式相同，但是协议不同，需要用第三个参数指定具体的协议信息。

**验证TCP字节流无边界(多次调用read读取)**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>
void error_handling(char *message);

int main(int argc, char *argv[])
{
    int sock;
    struct sockaddr_in serv_addr;
    char message[30];
    int str_len = 0;
    int idx = 0, read_len = 0;

    if (argc != 3)
    {
        printf("Usage : %s <IP> <port>\n", argv[0]);
        exit(1);
    }
    //创建套接字，此时套接字并不马上分为服务端和客户端。如果紧接着调用 bind,listen 函数，将成为服务器套接字
    //如果调用 connect 函数，将成为客户端套接字
    //若前两个参数使用PF_INET 和 SOCK_STREAM，则可以省略第三个参数 IPPROTO_TCP
    sock = socket(PF_INET, SOCK_STREAM, 0);
    if (sock == -1)
        error_handling("socket() error");

    memset(&serv_addr, 0, sizeof(serv_addr));
    serv_addr.sin_family = AF_INET;
    serv_addr.sin_addr.s_addr = inet_addr(argv[1]);
    serv_addr.sin_port = htons(atoi(argv[2]));
    //调用 connect 函数向服务器发送连接请求
    if (connect(sock, (struct sockaddr *)&serv_addr, sizeof(serv_addr)) == -1)
        error_handling("connect() error!");
    //当read函数返回0的时候条件为假，跳出循环。
    while (read_len = read(sock, &message[idx++], 1))
    {
        if (read_len == -1)
            error_handling("read() error!");
        str_len += read_len;
    }
    printf("Message from server : %s \n", message);
    printf("Function read call count: %d \n", str_len);
    close(sock);
    return 0;
}

void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

### 2.3 习题

***下面那些是面向消息的套接字的特性？*** 

+ **传输数据可能丢失**
+ 没有数据边界（Boundary）
+ **以快速传递为目标**
+ 不限制每次传输数据大小
+ **与面向连接的套接字不同，不存在连接概念**

## 第 3 章 地址族与数据序列

### 3.2 地址信息的表示

**表示 IPV4 地址的结构体**

```c
struct sockaddr_in
{
    sa_family_t sin_family; //地址族（Address Family）
    uint16_t sin_port; //16 位 TCP/UDP 端口号
    struct in_addr sin_addr; //32位 IP 地址
    char sin_zero[8]; //不使用
};
```

该结构体中提到的另一个结构体 in_addr 定义如下，它用来存放 32 位IP地址

```c
struct in_addr
{
    in_addr_t s_addr; //32位IPV4地址
}
```

关于以上两个结构体的一些数据类型。

| 数据类型名称 | 数据类型说明                         | 声明的头文件 |
| ------------ | ------------------------------------ | ------------ |
| int8_t       | signed 8-bit int                     | sys/types.h  |
| uint8_t      | unsigned 8-bit int (unsigned char)   | sys/types.h  |
| int16_t      | int16_t signed 16-bit int            | sys/types.h  |
| uint16_t     | unsigned 16-bit int (unsigned short) | sys/types.h  |
| int32_t      | signed 32-bit int                    | sys/types.h  |
| uint32_t     | unsigned 32-bit int (unsigned long)  | sys/types.h  |
| sa_family_t  | 地址族（address family）             | sys/socket.h |
| socklen_t    | 长度（length of struct）             | sys/socket.h |
| in_addr_t    | IP地址，声明为 uint_32_t             | netinet/in.h |
| in_port_t    | 端口号，声明为 uint_16_t             | netinet/in.h |

**成员 sin_family**

| 地址族（Address Family） | 含义                               |
| ------------------------ | ---------------------------------- |
| AF_INET                  | IPV4用的地址族                     |
| AF_INET6                 | IPV6用的地址族                     |
| AF_LOCAL                 | 本地通信中采用的 Unix 协议的地址族 |

**成员 sin_port**

该成员保存 16 位端口号，重点在于，它以**网络字节序**保存。

**成员 sin_addr**

该成员保存 32 为IP地址信息，且也以**网络字节序**保存。

**成员 sin_zero**

无特殊含义。只是为结构体 sockaddr_in 结构体变量地址值将以如下方式传递给 bind 函数。

`if (bind(serv_sock, (struct sockaddr *)&serv_addr, sizeof(serv_addr)) == -1)`

此处 bind 第二个参数期望得到的是 sockaddr 结构体变量的地址值，包括地址族、端口号、IP地 址等。

```C
struct sockaddr
{
    sa_family_t sin_family; //地址族
    char sa_data[14]; //地址信息
}
```

### 3.3 网络字节序与地址变换

大端序（Big Endian）：高位字节存放到低位地址**（网络序）**

小端序（Little Endian）：高位字节存放到高位地址

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed//202311291611418.png)

**字节序转换**

```c
unsigned short htons(unsigned short);
unsigned short ntohs(unsigned short);
unsigned long htonl(unsigned long);
unsigned long ntohl(unsigned long);
```

**实例程序**

```c
#include <stdio.h>
#include <arpa/inet.h>
int main(int argc, char *argv[])
{
    unsigned short host_port = 0x1234;
    unsigned short net_port;
    unsigned long host_addr = 0x12345678;
    unsigned long net_addr;
    net_port = htons(host_port); //转换为网络字节序
    net_addr = htonl(host_addr);
    printf("Host ordered port: %#x \n", host_port);
    printf("Network ordered port: %#x \n", net_port);
    printf("Host ordered address: %#lx \n", host_addr);
    printf("Network ordered address: %#lx \n", net_addr);
    return 0;
}
```

除了向sockaddr_in结构体变量填充数据外，其他情况无需考虑字节序问题。

### 3.4 网络地址的初始化与分配

`in_addr_t inet_addr(const char *string);`

会将点分十进制转换为网络序，转换失败时返回 INADDR_NONE。

`int inet_aton(const char *string, struct in_addr *addr);`

inet_aton 函数与 inet_addr 函数在功能上完全相同，也是将字符串形式的IP地址转换成整数型的IP地 址。只不过该函数用了 in_addr 结构体，且使用频率更高。

总结如下：

+ In_addr_t inet_addr(string)：ipv4 十进制转网络字节序（4 字节整数），返回值为socketaddr_in.in_addr.s_addr，类型为 in_addr_t，是存 ipv4 的变量，本质是个 uint_32t

+ Int inet_aton(string, &in_addr)：功能同上，但是这个函数更常用，因为更方便，引用传参直接把 ip 地址整数赋值给 socketaddr_in.in_addr

+ Inet_ntoa：网络字节序转十进制字符串

+ Inet_network：十进制转本地字节序

Inet_ntoa：该函数将通过参数传入的整数型IP地址转换为字符串格式并返回。但要小心，返回值为 char 指针，返回字符串地址意味着字符串已经保存在内存空间，但是该函数未向程序员要求分配内存，而是在内部申请了内存保存了字符串。也就是说调用了该函数候要立即把信息复制到其他内存空间。因此，若再次调用 inet_ntoa 函数，则有可能覆盖之前保存的字符串信息。总之，再次调用 inet_ntoa 函数前返回的字符串地址是有效的。若需要长期保存，则应该将字符串复制到其他内存空间。

利用常数INADDR_ANY分配服务器端的IP地址，则可自动获取运行服务器端的计算机IP地址，不必亲自输入。而且，若同一计算机中已分配多个IP地址（多宿主( Multi-homed )计算机，一般路由器属于这一类)，则只要端口号一致，就可以从不同IP地址接收数据。因此，服务器端中优先考虑这种方式。而客户端中除非带有一部分服务器端功能,否则不会采用。

## 第 4 章 基于 TCP 的服务端/客户端(1)

### 4.2 实现基于 TCP 的服务器端/客户端

**listen 函数**

```C
#include <sys/socket.h>
int listen(int sockfd, int backlog);
// 成功时返回0，失败时返回-1
// sock: 希望进入等待连接请求状态的套接字文件描述符，传递的描述符套接字参数称为服务端套接字
// backlog: 连接请求等待队列的长度，若为5，则队列长度为5，表示最多使5个连接请求进入队列
```

**accept 函数**

```C
#include <sys/socket.h>
int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
/*
    成功时返回文件描述符，失败时返回-1
    sock: 服务端套接字的文件描述符
    addr: 保存发起连接请求的客户端地址信息的变量地址值
    addrlen: 第二个参数addr结构体的长度，是存放长度的变量地址
*/
```

**connect 函数**

```C
#include <sys/socket.h>
int connect(int sock, struct sockaddr *servaddr, socklen_t addrlen);
/*
    成功时返回0，失败返回-1
    sock:客户端套接字文件描述符
    servaddr: 保存目标服务器端地址信息的变量地址值
    addrlen: 以字节为单位传递给第二个结构体参数 servaddr 的变量地址长度
*/
```

注意：**接受连接**不代表服务端调用 accept 函数，其实只是服务器端把连接请求信息记录到等待队列。 因此 connect 函数返回后并不应该立即进行数据交换。

实现服务器端必经过程之一就是给套接字分配IP和端口号。但客户端实现过程中并未出现套接字地址分配，而是创建套接字后立即调用connect函数。难道客户端套接字无需分配P和端口?当然不是!网络数据交换必须分配IP和端口。**既然如此，那客户端套接字何时、何地、如何分配地址呢?**

**何时? 调用connect函数时。**

**何地? 操作系统，更准确地说是在内核中。**

**如何? IP用计算机（主机）的IP，端口随机。**

客户端的IP地址和端口在调用connect函数时自动分配，无需调用标记的bind函数进行分配。



**针对套接字调用 close 函数，向连接的相应套接字发送 EOF。**

### 4.3 实现迭代服务端/客户端

**服务端**

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>

#define BUF_SIZE 1024
void error_handling(char *message);

int main(int argc, char *argv[])
{
    int serv_sock, clnt_sock;
    char message[BUF_SIZE];
    int str_len, i;

    struct sockaddr_in serv_adr, clnt_adr;
    socklen_t clnt_adr_sz;

    if (argc != 2)
    {
        printf("Usage : %s <port>\n", argv[0]);
        exit(1);
    }

    serv_sock = socket(PF_INET, SOCK_STREAM, 0);
    if (serv_sock == -1)
        error_handling("socket() error");

    memset(&serv_adr, 0, sizeof(serv_adr));
    serv_adr.sin_family = AF_INET;
    serv_adr.sin_addr.s_addr = htonl(INADDR_ANY);
    serv_adr.sin_port = htons(atoi(argv[1]));

    if (bind(serv_sock, (struct sockaddr *)&serv_adr, sizeof(serv_adr)) == -1)
        error_handling("bind() error");

    if (listen(serv_sock, 5) == -1)
        error_handling("listen() error");

    clnt_adr_sz = sizeof(clnt_adr);
    //调用 5 次 accept 函数，共为 5 个客户端提供服务
    for (i = 0; i < 5; i++)
    {
        clnt_sock = accept(serv_sock, (struct sockaddr *)&clnt_adr, &clnt_adr_sz);
        if (clnt_sock == -1)
            error_handling("accept() error");
        else
            printf("Connect client %d \n", i + 1);

        while ((str_len = read(clnt_sock, message, BUF_SIZE)) != 0)
            write(clnt_sock, message, str_len);

        close(clnt_sock);
    }
    close(serv_sock);
    return 0;
}

void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

**客户端**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>

#define BUF_SIZE 1024
void error_handling(char *message);

int main(int argc, char *argv[])
{
    int sock;
    char message[BUF_SIZE];
    int str_len;
    struct sockaddr_in serv_adr;

    if (argc != 3)
    {
        printf("Usage : %s <IP> <port>\n", argv[0]);
        exit(1);
    }

    sock = socket(PF_INET, SOCK_STREAM, 0);
    if (sock == -1)
        error_handling("socket() error");

    memset(&serv_adr, 0, sizeof(serv_adr));
    serv_adr.sin_family = AF_INET;
    serv_adr.sin_addr.s_addr = inet_addr(argv[1]);
    serv_adr.sin_port = htons(atoi(argv[2]));

    if (connect(sock, (struct sockaddr *)&serv_adr, sizeof(serv_adr)) == -1)
        error_handling("connect() error!");
    else
        puts("Connected...........");

    while (1)
    {
        fputs("Input message(Q to quit): ", stdout);
        fgets(message, BUF_SIZE, stdin);

        if (!strcmp(message, "q\n") || !strcmp(message, "Q\n"))
            break;

        write(sock, message, strlen(message));
        str_len = read(sock, message, BUF_SIZE - 1);
        message[str_len] = 0;
        printf("Message from server: %s", message);
    }
    close(sock);
    return 0;
}

void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

**回声客户端存在的问题**

以上代码有一个假设「每次调用 read、write函数时都会以字符串为单位执行实际 I/O 操作」 

但是「第二章」中说过「TCP 不存在数据边界」，上述客户端是基于 TCP 的，因此多次调用 write 函数传递的字符串有可能 一次性传递到服务端。此时客户端有可能从服务端收到多个字符串，这不是我们想要的结果。还需要考虑服务器的如下情况： 

「字符串太长，需要分 2 个包发送！」 

服务端希望通过调用 1 次 write 函数传输数据，但是如果数据太大，操作系统就有可能把数据分成多个 数据包发送到客户端。另外，在此过程中，客户端可能在尚未收到全部数据包时就调用 read 函数。

## 第 5 章 基于 TCP 的服务端/客户端(2)

### 5.1 回声客户端的完美实现

**回声客户端问题的解决办法**

这个问题其实很容易解决，因为可以知道提前接受数据的大小。若之前传输了20字节长的字符串，则再接收时循环调用 read 函数读取 20 个字节即可。

**如果问题不在于回声客户端**

回声客户端可以提前知道接收数据的长度，这在大多数情况下是不可能的。那么此时无法预知接收数据 长度时应该如何手法数据？这是需要的是**应用层协议的定义**。

**计算器服务器端/客户端示例**

**客户端**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>
#define BUF_SIZE 1024
#define RLT_SIZE 4 //字节大小数
#define OPSZ 4
void error_handling(char *message);

int main(int argc, char *argv[])
{
    int sock;
    char opmsg[BUF_SIZE];
    int result, opnd_cnt, i;
    struct sockaddr_in serv_adr;
    if (argc != 3)
    {
        printf("Usage : %s <IP> <port>\n", argv[0]);
        exit(1);
    }

    sock = socket(PF_INET, SOCK_STREAM, 0);
    if (sock == -1)
        error_handling("socket() error");

    memset(&serv_adr, 0, sizeof(serv_adr));
    serv_adr.sin_family = AF_INET;
    serv_adr.sin_addr.s_addr = inet_addr(argv[1]);
    serv_adr.sin_port = htons(atoi(argv[2]));

    if (connect(sock, (struct sockaddr *)&serv_adr, sizeof(serv_adr)) == -1)
        error_handling("connect() error!");
    else
        puts("Connected...........");

    fputs("Operand count: ", stdout);
    scanf("%d", &opnd_cnt);
    opmsg[0] = (char)opnd_cnt;

    for (i = 0; i < opnd_cnt; i++)
    {
        printf("Operand %d: ", i + 1);
        scanf("%d", (int *)&opmsg[i * OPSZ + 1]);
    }
    fgetc(stdin);
    fputs("Operator: ", stdout);
    scanf("%c", &opmsg[opnd_cnt * OPSZ + 1]);
    write(sock, opmsg, opnd_cnt * OPSZ + 2);
    read(sock, &result, RLT_SIZE);

    printf("Operation result: %d \n", result);
    close(sock);
    return 0;
}

void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

**服务端**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>
#define BUF_SIZE 1024
#define OPSZ 4
void error_handling(char *message);
int calculate(int opnum, int opnds[], char oprator);
int main(int argc, char *argv[])
{
    int serv_sock, clnt_sock;
    char opinfo[BUF_SIZE];
    int result, opnd_cnt, i;
    int recv_cnt, recv_len;
    struct sockaddr_in serv_adr, clnt_adr;
    socklen_t clnt_adr_sz;
    if (argc != 2)
    {
        printf("Usage : %s <port>\n", argv[0]);
        exit(1);
    }

    serv_sock = socket(PF_INET, SOCK_STREAM, 0);
    if (serv_sock == -1)
        error_handling("socket() error");

    memset(&serv_adr, 0, sizeof(serv_adr));
    serv_adr.sin_family = AF_INET;
    serv_adr.sin_addr.s_addr = htonl(INADDR_ANY);
    serv_adr.sin_port = htons(atoi(argv[1]));

    if (bind(serv_sock, (struct sockaddr *)&serv_adr, sizeof(serv_adr)) == -1)
        error_handling("bind() error");
    if (listen(serv_sock, 5) == -1)
        error_handling("listen() error");
    clnt_adr_sz = sizeof(clnt_adr);
    for (int i = 0; i < 5; i++)
    {
        opnd_cnt = 0;
        clnt_sock = accept(serv_sock, (struct sockaddr *)&clnt_adr, &clnt_adr_sz);
        read(clnt_sock, &opnd_cnt, 1);

        recv_len = 0;
        while ((opnd_cnt * OPSZ + 1) > recv_len)
        {
            recv_cnt = read(clnt_sock, &opinfo[recv_len], BUF_SIZE - 1);
            recv_len += recv_cnt;
        }
        result = calculate(opnd_cnt, (int *)opinfo, opinfo[recv_len - 1]);
        write(clnt_sock, (char *)&result, sizeof(result));
        close(clnt_sock);
    }
    close(serv_sock);
    return 0;
}
int calculate(int opnum, int opnds[], char op)
{
    int result = opnds[0], i;
    switch (op)
    {
    case '+':
        for (i = 1; i < opnum; i++)
            result += opnds[i];
        break;
    case '-':
        for (i = 1; i < opnum; i++)
            result -= opnds[i];
        break;
    case '*':
        for (i = 1; i < opnum; i++)
            result *= opnds[i];
        break;
    }
    return result;
}
void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

### 5.2 TCP原理

I/O 缓冲特性可以整理如下： 

+ I/O 缓冲在每个 TCP 套接字中单独存在 
+ I/O 缓冲在创建套接字时自动生成 
+ 即使关闭套接字也会继续传递输出缓冲中遗留的数据 
+ 关闭套接字将丢失输入缓冲中的数据

## 第 6 章 基于 UDP 的服务端/客户端

### 6.2 实现基于 UDP 的服务端/客户端

**基于 UDP 的数据 I/O 函数**

**发送函数**

```c
#include <sys/socket.h>
ssize_t sendto(int sock, void *buff, size_t nbytes, int flags,
struct sockaddr *to, socklen_t addrlen);
/*
    成功时返回传输的字节数，失败是返回 -1
    sock: 用于传输数据的 UDP 套接字
    buff: 保存待传输数据的缓冲地址值
    nbytes: 待传输的数据长度，以字节为单位
    flags: 可选项参数，若没有则传递 0
    to: 存有目标地址的 sockaddr 结构体变量的地址值
    addrlen: 传递给参数 to 的地址值结构体变量长度
*/
```

**接收函数**

```c
#include <sys/socket.h>
ssize_t recvfrom(int sock, void *buff, size_t nbytes, int flags,
struct sockaddr *from, socklen_t *addrlen);
/*
    成功时返回传输的字节数，失败是返回 -1
    sock: 用于传输数据的 UDP 套接字
    buff: 保存待传输数据的缓冲地址值
    nbytes: 待传输的数据长度，以字节为单位
    flags: 可选项参数，若没有则传递 0
    from: 存有发送端地址信息的 sockaddr 结构体变量的地址值
    addrlen: 保存参数 from 的结构体变量长度的变量地址值。
*/
```

**基于 UDP 的回声服务器端/客户端**

**服务端**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>

#define BUF_SIZE 30
void error_handling(char *message);

int main(int argc, char *argv[])
{
    int serv_sock;
    char message[BUF_SIZE];
    int str_len;
    socklen_t clnt_adr_sz;

    struct sockaddr_in serv_adr, clnt_adr;
    if (argc != 2)
    {
        printf("Usage : %s <port>\n", argv[0]);
        exit(1);
    }
    //创建 UDP 套接字后，向 socket 的第二个参数传递 SOCK_DGRAM
    serv_sock = socket(PF_INET, SOCK_DGRAM, 0);
    if (serv_sock == -1)
        error_handling("UDP socket creation eerror");

    memset(&serv_adr, 0, sizeof(serv_adr));
    serv_adr.sin_family = AF_INET;
    serv_adr.sin_addr.s_addr = htonl(INADDR_ANY);
    serv_adr.sin_port = htons(atoi(argv[1]));
    //分配地址接受数据，不限制数据传输对象
    if (bind(serv_sock, (struct sockaddr *)&serv_adr, sizeof(serv_adr)) == -1)
        error_handling("bind() error");

    while (1)
    {
        clnt_adr_sz = sizeof(clnt_adr);
        str_len = recvfrom(serv_sock, message, BUF_SIZE, 0,
                           (struct sockaddr *)&clnt_adr, &clnt_adr_sz);
        //通过上面的函数调用同时获取数据传输端的地址。正是利用该地址进行逆向重传
        sendto(serv_sock, message, str_len, 0,
               (struct sockaddr *)&clnt_adr, clnt_adr_sz);
    }
    close(serv_sock);
    return 0;
}

void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

**客户端**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>

#define BUF_SIZE 30
void error_handling(char *message);

int main(int argc, char *argv[])
{
    int sock;
    char message[BUF_SIZE];
    int str_len;
    socklen_t adr_sz;

    struct sockaddr_in serv_adr, from_adr;
    if (argc != 3)
    {
        printf("Usage : %s <IP> <port>\n", argv[0]);
        exit(1);
    }
    //创建 UDP 套接字
    sock = socket(PF_INET, SOCK_DGRAM, 0);
    if (sock == -1)
        error_handling("socket() error");

    memset(&serv_adr, 0, sizeof(serv_adr));
    serv_adr.sin_family = AF_INET;
    serv_adr.sin_addr.s_addr = inet_addr(argv[1]);
    serv_adr.sin_port = htons(atoi(argv[2]));

    while (1)
    {
        fputs("Insert message(q to quit): ", stdout);
        fgets(message, sizeof(message), stdin);
        if (!strcmp(message, "q\n") || !strcmp(message, "Q\n"))
            break;
        //向服务器传输数据,会自动给自己分配IP地址和端口号
        sendto(sock, message, strlen(message), 0,
               (struct sockaddr *)&serv_adr, sizeof(serv_adr));
        adr_sz = sizeof(from_adr);
        str_len = recvfrom(sock, message, BUF_SIZE, 0,
                           (struct sockaddr *)&from_adr, &adr_sz);
        message[str_len] = 0;
        printf("Message from server: %s", message);
    }
    close(sock);
    return 0;
}

void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

**UDP 客户端套接字的地址分配**

仔细观察 UDP 客户端可以发现，UDP 客户端缺少了把IP和端口分配给套接字的过程。TCP 客户端调用 connect 函数自动完成此过程，而 UDP 中连接能承担相同功能的函数调用语句都没有。究竟在什么时候分配IP和端口号呢？

UDP 程序中，调用 sendto 函数传输数据前应该完成对套接字的地址分配工作，因此调用 bind 函数。当然，bind 函数在 TCP 程序中出现过，但 bind 函数不区分 TCP 和 UDP，也就是说，在 UDP 程序中 同样可以调用。另外，**如果调用 sendto 函数尚未分配地址信息，则在首次调用 sendto 函数时给相应套接字自动分配 IP 和端口。而且此时分配的地址一直保留到程序结束为止，因此也可以用来和其他 UDP 套接字进行数据交换。当然，IP 用主机IP，端口号用未选用的任意端口号。**

综上所述，调用 sendto 函数时自动分配IP和端口号，因此，UDP 客户端中通常无需额外的地址分配过 程。所以之前的示例中省略了该过程。这也是普遍的实现方式。

### 6.3 UDP 的数据传输特性和调用 connect 函数

**已连接（connect）UDP 套接字与未连接（unconnected）UDP 套接字**

TCP 套接字中需注册待传传输数据的目标IP和端口号（connect），而在 UDP 中无需注册。因此通过 sendto 函数传输数据的过程大概可以分为以下 3 个阶段：

+ 第 1 阶段：向 UDP 套接字注册目标 IP 和端口号 

+ 第 2 阶段：传输数据 

+ 第 3 阶段：删除 UDP 套接字中注册的目标地址信息

每次调用 sendto 函数时重复上述过程。每次都变更目标地址，因此可以重复利用同一 UDP 套接字向不同目标传递数据。这种未注册目标地址信息的套接字称为未连接套接字，反之，注册了目标地址的套接字称为连接 connected 套接字。显然，UDP 套接字默认属于未连接套接字。当一台主机向另一台主机传输很多信息时，上述的三个阶段中，第一个阶段和第三个阶段占整个通信过程中近三分之一的时间，缩短这部分的时间将会大大提高整体性能。

如果多次向同一个 ip + port 传输 UDP 数据，未连接套接字会浪费时间。

**创建已连接 UDP 套接字**

创建已连接 UDP 套接字过程格外简单，只需针对 UDP 套接字调用 connect 函数。

```c
sock = socket(PF_INET, SOCK_DGRAM, 0);
memset(&adr, 0, sizeof(adr));
adr.sin_family = AF_INET;
adr.sin_addr.s_addr = inet_addr(argv[1]);
adr.sin_port = htons(atoi(argv[2]));
connect(sock, (struct sockaddr *)&adr, sizeof(adr));
```

**当然针对 UDP 调用 connect 函数并不是意味着要与对方 UDP 套接 字连接，这只是向 UDP 套接字注册目标IP和端口信息。之后就与 TCP 套接字一致，每次调用 sendto 函数时只需传递信息数据。**

**因为已经指定了收发对象，所以不仅可以使用 sendto、recvfrom 函数，还可以使用 write、read 函数进行通信。**

### 6.5 习题

***UDP 一般比 TCP 快，但根据交换数据的特点，其差异可大可小。请你说明何种情况下 UDP 的性能优于 TCP？***

如果收发数据量小但需要频繁连接时，UDP 比 TCP 更高效。

UDP 存在数据边界，如果数据量太大则会分片传输。

***客户端 TCP 套接字调用 connect 函数时自动分配IP和端口号。UDP 中不调用 bind 函数，那何时分配IP和端口号？*** 

答：在首次调用 sendto 函数时自动给相应的套接字分配IP和端口号。而且此时分配的地址一直保 留到程序结束为止。

***TCP 客户端必须调用 connect 函数，而 UDP 可以选择性调用。请问，在 UDP 中调用 connect 函数有哪些好处？***

答：要与同一个主机进行长时间通信时，将 UDP 套接字变成已连接套接字会提高效率。因为三个阶段中，第一个阶段和第三个阶段占用了一大部分时间，调用 connect 函数可以节省这些时间。

 TCP 套接字中需注册待传传输数据的目标IP和端口号，而在 UDP 中无需注册。因此通过 sendto 函数传输数据的过程大概可以分为以下 3 个阶段：

+ 第 1 阶段：向 UDP 套接字注册目标 IP 和端口号
+ 第 2 阶段：传输数据
+ 第 3 阶段：删除 UDP 套接字中注册的目标地址信息。

## 第 7 章 优雅的断开套接字的连接

### 7.1 基于 TCP 的半关闭

Linux 和 Windows 的 closesocket 函数意味着完全断开连接。完全断开不仅指无法传输数据，而且也不能接收数据。因此在某些情况下，通信一方单方面的断开套接字连接，显得不太优雅。

**针对优雅断开的 shutdown 函数**

```c
#include <sys/socket.h>
int shutdown(int sock, int howto);
/*
    成功时返回 0 ，失败时返回 -1
    sock: 需要断开套接字文件描述符
    howto: 传递断开方式信息
*/
```

shutdown 用来关闭其中一个流，调用上述函数时，第二个参数决定断开连接的方式，其值如下所示： 

+ SHUT_RD : 断开输入流 
+ SHUT_WR : 断开输出流 
+ SHUT_RDWR : 同时断开 I/O 流

**基于半关闭的文件传输程序**

**服务端**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>

#define BUF_SIZE 30
void error_handling(char *message);

int main(int argc, char *argv[])
{
    int serv_sd, clnt_sd;
    FILE *fp;
    char buf[BUF_SIZE];
    int read_cnt;

    struct sockaddr_in serv_adr, clnt_adr;
    socklen_t clnt_adr_sz;

    if (argc != 2)
    {
        printf("Usage : %s <port>\n", argv[0]);
        exit(1);
    }
    fp = fopen("file_server.c", "rb");
    serv_sd = socket(PF_INET, SOCK_STREAM, 0);

    memset(&serv_adr, 0, sizeof(serv_adr));
    serv_adr.sin_family = AF_INET;
    serv_adr.sin_addr.s_addr = htonl(INADDR_ANY);
    serv_adr.sin_port = htons(atoi(argv[1]));

    bind(serv_sd, (struct sockaddr *)&serv_adr, sizeof(serv_adr));
    listen(serv_sd, 5);

    clnt_adr_sz = sizeof(clnt_adr);
    clnt_sd = accept(serv_sd, (struct sockaddr *)&clnt_adr, &clnt_adr_sz);

    while (1)
    {
        //从文件流中读取数据，buffer为接收数据的地址，size为一个单元的大小，count为单元个数，stream为文件流
        //返回实际读取的单元个数
        read_cnt = fread((void *)buf, 1, BUF_SIZE, fp);
        if (read_cnt < BUF_SIZE)
        {
            write(clnt_sd, buf, read_cnt);
            break;
        }
        write(clnt_sd, buf, BUF_SIZE);
    }

    shutdown(clnt_sd, SHUT_WR);
    read(clnt_sd, buf, BUF_SIZE);
    printf("Message from client: %s \n", buf);

    fclose(fp);
    close(clnt_sd);
    close(serv_sd);
    return 0;
}

void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

**客户端**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>

#define BUF_SIZE 30
void error_handling(char *message);

int main(int argc, char *argv[])
{
    int sd;
    FILE *fp;

    char buf[BUF_SIZE];
    int read_cnt;
    struct sockaddr_in serv_adr;

    if (argc != 3)
    {
        printf("Usage : %s <IP> <port>\n", argv[0]);
        exit(1);
    }

    fp = fopen("receive.cpp", "wb");
    sd = socket(PF_INET, SOCK_STREAM, 0);

    memset(&serv_adr, 0, sizeof(serv_adr));
    serv_adr.sin_family = AF_INET;
    serv_adr.sin_addr.s_addr = inet_addr(argv[1]);
    serv_adr.sin_port = htons(atoi(argv[2]));

    connect(sd, (struct sockaddr *)&serv_adr, sizeof(serv_adr));

    while ((read_cnt = read(sd, buf, BUF_SIZE)) != 0)
        fwrite((void *)buf, 1, read_cnt, fp);

    puts("Received file data");
    write(sd, "Thank you", 10);
    fclose(fp);
    close(sd);
    return 0;
}

void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

半关闭会导致对方主机接收一个 EOF 文件结束符。对方就知道你的数据已经传输完 毕。

## 第 8 章 域名及网络地址

### 8.2 IP地址和域名之间的转换

**利用域名获取 IP 地址**

```c
#include <netdb.h>
struct hostent *gethostbyname(const char *hostname);
/*
    成功时返回 hostent 结构体地址，失败时返回 NULL 指针
*/
```

这个函数使用方便，只要传递字符串，就可以返回域名对应的IP地址。只是返回时，地址信息装入 hostent 结构体。此结构体的定义如下：

```c
struct hostent
{
    char *h_name; /* Official name of host. */
    char **h_aliases; /* Alias list. */
    int h_addrtype; /* Host address type. */
    int h_length; /* Length of address. */
    char **h_addr_list; /* List of addresses from name server. */
};
```

从上述结构体可以看出，不止返回IP信息，同时还带着其他信息一起返回。域名转换成IP时只需要关注 h_addr_list 。下面简要说明上述结构体的成员： 

+ h_name：该变量中存有官方域名（Official domain name）。官方域名代表某一主页，但实际上，一些著名公司的域名并没有用官方域名注册。 
+ h_aliases：可以通过多个域名访问同一主页。同一IP可以绑定多个域名，因此，除官方域名外还可以指定其他域名。这些信息可以通过 h_aliases 获得。 
+ h_addrtype：gethostbyname 函数不仅支持 IPV4 还支持 IPV6 。因此可以通过此变量获取保存在 h_addr_list 的IP地址族信息。若是 IPV4 ，则此变量中存有 AF_INET。 
+ h_length：保存IP地址长度。若是 IPV4 地址，因为是 4 个字节，则保存4；IPV6 时，因为是 16 个字节，故保存 16 
+ h_addr_list：这个是最重要的的成员。通过此变量以整数形式保存域名相对应的IP地址。另外，用户比较多的网站有可能分配多个IP地址给同一个域名，利用多个服务器做负载均衡，此时可以通过此变量获取IP地址信息。

调用 gethostbyname 函数后，返回的结构体变量如图所示：

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed//202312010942419.png)

**演示 gethostbyname 函数**

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <netdb.h>
void error_handling(char *message);

int main(int argc, char *argv[])
{
    int i;
    struct hostent *host;
    if (argc != 2)
    {
        printf("Usage : %s <addr>\n", argv[0]);
        exit(1);
    }
    // 把参数传递给函数，返回结构体
    host = gethostbyname(argv[1]);
    if (!host)
        error_handling("gethost... error");
    // 输出官方域名
    printf("Official name: %s \n", host->h_name);
    // Aliases 貌似是解析的 cname 域名？
    for (i = 0; host->h_aliases[i]; i++)
        printf("Aliases %d: %s \n", i + 1, host->h_aliases[i]);
    //看看是不是ipv4
    printf("Address type: %s \n",
           (host->h_addrtype == AF_INET) ? "AF_INET" : "AF_INET6");
    // 输出ip地址信息
    for (i = 0; host->h_addr_list[i]; i++)
        printf("IP addr %d: %s \n", i + 1,
               inet_ntoa(*(struct in_addr *)host->h_addr_list[i]));
    return 0;
}
void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

仔细阅读这一段代码：

` inet_ntoa(*(struct in_addr *)host->h_addr_list[i])`

若只看 hostent 的定义，结构体成员 h_addr_list 指向字符串指针数组（由多个字符串地址构成的数组）。但是字符串指针数组保存的元素实际指向的是 in_addr 结构体变量中地址值而非字符串，也就是说 (struct in_addr *)host->h_addr_list[i] 其实是一个指针，然后用 * 符号取具体的值。

不用 void 指针类型是因为套接字相关函数都是在 void 指针标准化之前定义的，而当时无法明确指出指针类型时采用的都是 char 指针。

**利用 IP 地址获取域名**

```c
#include <netdb.h>
struct hostent *gethostbyaddr(const char *addr, socklen_t len, int family);
/*
    成功时返回 hostent 结构体变量地址值，失败时返回 NULL 指针
    addr: 含有IP地址信息的 in_addr 结构体指针。为了同时传递 IPV4 地址之外的全部信息，该变量
    的类型声明为 char 指针
    len: 向第一个参数传递的地址信息的字节数，IPV4时为4 ，IPV6 时为16.
    family: 传递地址族信息，ipv4 是 AF_INET ，IPV6是 AF_INET6
*/
```

**演示 gethostbyaddr函数**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <netdb.h>
void error_handling(char *message);

int main(int argc, char *argv[])
{
    int i;
    struct hostent *host;
    struct sockaddr_in addr;
    if (argc != 2)
    {
        printf("Usage : %s <IP>\n", argv[0]);
        exit(1);
    }

    memset(&addr, 0, sizeof(addr));
    addr.sin_addr.s_addr = inet_addr(argv[1]);
    host = gethostbyaddr((char *)&addr.sin_addr, 4, AF_INET);
    if (!host)
        error_handling("gethost... error");

    printf("Official name: %s \n", host->h_name);
    for (i = 0; host->h_aliases[i]; i++)
        printf("Aliases %d:%s \n", i + 1, host->h_aliases[i]);
    printf("Address type: %s \n",
           (host->h_addrtype == AF_INET) ? "AF_INET" : "AF_INET6");

    for (i = 0; host->h_addr_list[i]; i++)
        printf("IP addr %d: %s \n", i + 1,
               inet_ntoa(*(struct in_addr *)host->h_addr_list[i]));

    return 0;
}
void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

## 第 9 章 套接字的多种可选项

### 9.1 套接字可选项和 I/O 缓冲大小

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed//202312011039083.png)

从表中可以看出，套接字可选项是分层的。

+ IPPROTO_IP 可选项是IP协议相关事项 
+ IPPROTO_TCP 层可选项是 TCP 协议的相关事项 
+ SOL_SOCKET 层是套接字的通用可选项。

**getsockopt & setsockopt**

```c
#include <sys/socket.h>
int getsockopt(int sock, int level, int optname, void *optval, socklen_t
*optlen);
/*
    成功时返回 0 ，失败时返回 -1
    sock: 用于查看选项套接字文件描述符
    level: 要查看的可选项协议层
    optname: 要查看的可选项名
    optval: 保存查看结果的缓冲地址值
    optlen: 向第四个参数传递的缓冲大小。调用函数候，该变量中保存通过第四个参数返回的可选项信
    息的字节数。
*/

int setsockopt(int sock, int level, int optname, const void *optval,
socklen_t optlen);
/*
    成功时返回 0 ，失败时返回 -1
    sock: 用于更改选项套接字文件描述符
    level: 要更改的可选项协议层
    optname: 要更改的可选项名
    optval: 保存更改结果的缓冲地址值
    optlen: 向第四个参数传递的缓冲大小。调用函数候，该变量中保存通过第四个参数返回的可选项信
    息的字节数。
*/

```

**getsockopt 的使用方法示例**

下面示例用协议层为 SOL_SOCKET 、名为 SO_TYPE 的 可选项查看套接字类型（TCP 和 UDP ）。

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/socket.h>
void error_handling(char *message);

int main(int argc, char *argv[])
{
    int tcp_sock, udp_sock;
    int sock_type;
    socklen_t optlen;
    int state;

    optlen = sizeof(sock_type);
    tcp_sock = socket(PF_INET, SOCK_STREAM, 0);
    udp_sock = socket(PF_INET, SOCK_DGRAM, 0);
    printf("SOCK_STREAM: %d\n", SOCK_STREAM);
    printf("SOCK_DGRAM: %d\n", SOCK_DGRAM);

    state = getsockopt(tcp_sock, SOL_SOCKET, SO_TYPE, (void *)&sock_type, &optlen);
    if (state)
        error_handling("getsockopt() error");
    printf("Socket type one: %d \n", sock_type);

    state = getsockopt(udp_sock, SOL_SOCKET, SO_TYPE, (void *)&sock_type, &optlen);
    if (state)
        error_handling("getsockopt() error");
    printf("Socket type two: %d \n", sock_type);
    return 0;
}
void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

**SO_SNDBUF & SO_RCVBUF**

**读取套接字默认 I/O 缓冲区大小示例**

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/socket.h>
void error_handling(char *message);

int main(int argc, char *argv[])
{
    int sock;
    int snd_buf, rcv_buf, state;
    socklen_t len;

    sock = socket(PF_INET, SOCK_STREAM, 0);
    len = sizeof(snd_buf);
    state = getsockopt(sock, SOL_SOCKET, SO_SNDBUF, (void *)&snd_buf, &len);
    if (state)
        error_handling("getsockopt() error");

    len = sizeof(rcv_buf);
    state = getsockopt(sock, SOL_SOCKET, SO_RCVBUF, (void *)&rcv_buf, &len);
    if (state)
        error_handling("getsockopt() error");

    printf("Input buffer size: %d \n", rcv_buf);
    printf("Output buffer size: %d \n", snd_buf);

    return 0;
}
void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

**设置套接字默认 I/O 缓冲区大小示例**

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/socket.h>
void error_handling(char *message);

int main(int argc, char *argv[])
{
    int sock;
    int snd_buf = 1024 * 3, rcv_buf = 1024 * 3;
    int state;
    socklen_t len;

    sock = socket(PF_INET, SOCK_STREAM, 0);
    len = sizeof(snd_buf);
    state = setsockopt(sock, SOL_SOCKET, SO_RCVBUF, (void *)&rcv_buf, sizeof(rcv_buf));
    if (state)
        error_handling("setsockopt() error");

    state = setsockopt(sock, SOL_SOCKET, SO_SNDBUF, (void *)&snd_buf, sizeof(snd_buf));
    if (state)
        error_handling("setsockopt() error");

    len = sizeof(snd_buf);
    state = getsockopt(sock, SOL_SOCKET, SO_SNDBUF, (void *)&snd_buf, &len);
    if (state)
        error_handling("getsockopt() error");

    len = sizeof(rcv_buf);
    state = getsockopt(sock, SOL_SOCKET, SO_RCVBUF, (void *)&rcv_buf, &len);
    if (state)
        error_handling("getsockopt() error");

    printf("Input buffer size: %d \n", rcv_buf);
    printf("Output buffer size: %d \n", snd_buf);

    return 0;
}
void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

### 9.2 SO_REUSEADDR

服务器端和客户端都已经建立连接的状态下，向服务器控制台输入 CTRL+C ，强制关闭服务端。如果用这种方式终止程序，如果用同一端口号再次运行服务端，就会输出「bind() error」消息，并且无法再次运行。但是在这种情况下，再过大约 3 分钟就可以重新运行服务端。

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed//202312011345032.png)

**只有先断开连接的（先发送 FIN 消息的）主机才经过 Time-wait 状态。**因此，若服务器端先断开连接，则无法立即重新运行。套接字处在 Time-wait 过程时，相应端口是正在使用的状态。因此，就像之前验证过的，bind 函数调用过程中会发生错误。

**实际上，不论是服务端还是客户端，都要经过一段时间的 Time-wait 过程。先断开连接的套接字必然会经过 Time-wait 过程，但是由于客户端套接字的端口是任意制定的，所以无需过多关注 Time-wait 状态。**

Time-wait 状态看似重要，但是不一定讨人喜欢。如果系统发生故障紧急停止，这时需要尽快重启服务起以提供服务，但因处于 Time-wait 状态而必须等待几分钟。

解决方案就是在套接字的可选项中更改 SO_REUSEADDR 的状态。适当调整该参数，可将 Time-wait 状态下的套接字端口号重新分配给新的套接字。SO_REUSEADDR 的默认值为 0。这就意味着无法分配 Time-wait 状态下的套接字端口号。因此需要将这个值改成 1 。

 ```c
 optlen = sizeof(option);
 option = TRUE;
 setsockopt(serv_sock, SOL_SOCKET, SO_REUSEADDR, (void *)&option, optlen);
 ```

### 9.3 TCP_NODELAY

为了防止因数据包过多而发生网络过载， Nagle 算法诞生了。

TCP 套接字默认使用 Nagle 算法交换数据，因此最大限度的进行缓冲，直到收到 ACK。

Nagle 算法并不是什么情况下都适用，网络流量未受太大影响时，不使用 Nagle 算法要比使用它时传输速度快。最典型的就是「传输大文数据」。将文件数据传入输出缓冲不会花太多时间，因此，不使用 Nagle 算法，也会在装满输出缓冲时传输数据包。这不仅不会增加数据包的数量，反而在无需等待 ACK 的前提下连续传输，因此可以大大提高传输速度。 所以，未准确判断数据性质时不应禁用 Nagle 算法。

禁用 Nagle 算法应该使用：

```c
int opt_val = 1;
setsockopt(sock, IPPROTO_TCP, TCP_NODELAY, (void *)&opt_val,
sizeof(opt_val));
```

通过 TCP_NODELAY 的值来查看 Nagle 算法的设置状态。

```c
opt_len = sizeof(opt_val);
getsockopt(sock, IPPROTO_TCP, TCP_NODELAY, (void *)&opt_val, &opt_len);
```

如果正在使用 Nagle 算法，那么 opt_val 值为 0，如果禁用则为 1。

## 第 10 章 多进程服务器端

### 10.1 进程概念及应用

下面列出的是具有代表性的并发服务端的实现模型和方法： 

+ 多进程服务器：通过创建多个进程提供服务 

+ 多路复用服务器：通过捆绑并统一管理 I/O 对象提供服务 

+ 多线程服务器：通过生成与客户端等量的线程提供服务

创建进程的方式很多，此处只介绍用于创建多进程服务端的 fork 函数。

```c
#include <unistd.h>
pid_t fork(void);
// 成功时返回进程ID,失败时返回 -1
```

### 10.2 进程和僵尸进程

僵尸进程是当子进程比父进程先结束，而父进程又没有回收子进程，释放子进程占用的资源，此 时子进程将成为一个僵尸进程。如果父进程先退出 ，子进程被init接管，子进程退出后init会回收 其占用的相关资源。

UNIX命令ps列出的进程的状态（"STAT"）栏标示为 "Z"则为僵尸进程。

利用如下两个示例展示调用 fork 函数产生子进程的终止方式。 

+ 传递参数并调用 exit() 函数 

+ main 函数中执行 return 语句并返回值

**向 exit 函数传递的参数值和 main 函数的 return 语句返回的值都回传递给操作系统。而操作系统不会销毁子进程，直到把这些值传递给产生该子进程的父进程。处在这种状态下的进程就是僵尸进程。**

**销毁僵尸进程 1：利用 wait 函数**

为了销毁子进程，父进程应该主动请求获取子进程的返回值。下面是发起请求的具体方法。有两种，下面的函数是其中一种。

```c
#include <sys/wait.h>
pid_t wait(int *statloc);
/*
    成功时返回终止的子进程 ID ,失败时返回 -1
*/
```

调用此函数时如果已有子进程终止，那么子进程终止时传递的返回值（exit 函数的参数返回值，main 函数的 return 返回值）将保存到该函数的参数所指的内存空间。但函数参数指向的单元中还包含其他信息，因此需要用下列宏进行分离： 

+ WIFEXITED 子进程正常终止时返回「真」 
+ WEXITSTATUS 返回子进程时的返回值

也就是说，向 wait 函数传递变量 status 的地址时，调用 wait 函数后应编写如下代码：

```c
if (WIFEXITED(status))
{
    puts("Normal termination");
    printf("Child pass num: %d", WEXITSTATUS(status));
}
```

这就是通过 wait 函数消灭僵尸进程的方法，调用 wait 函数时，如果没有已经终止的子进程，那么程序将阻塞（Blocking）直到有子进程终止，因此要谨慎调用该函数。

**销毁僵尸进程 2：使用 waitpid 函数**

wait 函数会引起程序阻塞，还可以考虑调用 waitpid 函数。这是防止僵尸进程的第二种方法，也是防止阻塞的方法。

```c
#include <sys/wait.h>
pid_t waitpid(pid_t pid, int *statloc, int options);
/*
    成功时返回终止的子进程ID 或 0 ，失败时返回 -1
    pid: 等待终止的目标子进程的ID,若传 -1，则与 wait 函数相同，可以等待任意子进程终止
    statloc: 与 wait 函数的 statloc 参数具有相同含义
    options: 传递头文件 sys/wait.h 声明的常量 WNOHANG ,即使没有终止的子进程也不会进入阻塞
    状态，而是返回 0 退出函数。
*/
```

**waitpid 样例**

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>
int main(int argc, char *argv[])
{
    int status;
    pid_t pid = fork();
    if (pid == 0)
    {
        sleep(15); //用 sleep 推迟子进程的执行
        return 24;
    }
    else
    {
        //调用waitpid 传递参数 WNOHANG， 即使没有终止的子进程也不会进入阻塞状态，则返回0
        while (!waitpid(-1, &status, WNOHANG))
        {
            sleep(3);
            puts("sleep 3 sec.");
        }
        if (WIFEXITED(status))
            printf("Child send %d \n", WEXITSTATUS(status));
    }
    return 0;
}

```

### 10.3 信号处理

**信号注册函数**

```c
#include <signal.h>
void (*signal(int signo, void (*func)(int)))(int);
/*
    为了在产生信号时调用，返回之前注册的函数指针
    函数名: signal
    参数：int signo,void(*func)(int)
    返回类型：参数类型为int型，返回 void 型函数指针
*/

```

**alarm 函数**

```c
#include <unistd.h>
unsigned int alarm(unsigned int seconds);
// 返回0或以秒为单位的距 SIGALRM 信号发生所剩时间
```

如果调用该函数的同时向它传递一个正整型参数，相应时间后（以秒为单位）将产生 SIGALRM 信号。若向该函数传递为 0 ，则之前对 SIGALRM 信号的预约将取消。如果通过改函数预约信号后未指定该信号对应的处理函数，则（通过调用 signal 函数）终止进程，不做任何处理。

```c
#include <stdio.h>
#include <unistd.h>
#include <signal.h>

void timeout(int sig) //信号处理器
{
    if (sig == SIGALRM)
        puts("Time out!");
    alarm(2); //为了每隔 2 秒重复产生 SIGALRM 信号，在信号处理器中调用 alarm 函数
}
void keycontrol(int sig) //信号处理器
{
    if (sig == SIGINT)
        puts("CTRL+C pressed");
}
int main(int argc, char *argv[])
{
    int i;
    signal(SIGALRM, timeout); //注册信号及相应处理器
    signal(SIGINT, keycontrol);
    alarm(2); //预约 2 秒候发生 SIGALRM 信号

    for (i = 0; i < 3; i++)
    {
        puts("wait...");
        sleep(100);
    }
    return 0;
}
```

发生信号时将唤醒由于调用 sleep 函数而进入阻塞状态的进程。

调用函数的主题的确是操作系统，但是进程处于睡眠状态时无法调用函数，因此，产生信号时，为了调用信号处理器，将唤醒由于调用 sleep 函数而进入阻塞状态的进程。而且，进程一旦被唤醒，就不会再进入睡眠状态。即使还未到 sleep 中规定的时间也是如此。所以上述示例运行不到 10 秒后就会结束，连续输入 CTRL+C 可能连一秒都不到。

**利用 sigaction 函数进行信号处理**

前面所学的内容可以防止僵尸进程，还有一个函数，叫做 sigaction 函数，类似于 signal 函数，**而且可以完全代替后者**，也更稳定。之所以稳定，是因为：**signal 函数在 Unix 系列的不同操作系统可能存在区别，但 sigaction 函数完全相同。**

```c
#include <signal.h>
int sigaction(int signo, const struct sigaction *act, struct sigaction
*oldact);
/*
    成功时返回 0 ，失败时返回 -1
    act: 对于第一个参数的信号处理函数（信号处理器）信息。
    oldact: 通过此参数获取之前注册的信号处理函数指针，若不需要则传递 0
*/
```

声明并初始化 sigaction 结构体变量以调用上述函数，该结构体定义如下：

```c
struct sigaction
{
    void (*sa_handler)(int);
    sigset_t sa_mask;
    int sa_flags;
};
```

此结构体的成员 sa_handler 保存信号处理的函数指针值（地址值）。sa_mask 和 sa_flags 的所有位初始化 0 即可。这 2 个成员用于指定信号相关的选项和特性，而我们的目的主要是防止产生僵尸进程，故省略。

```c
#include <stdio.h>
#include <unistd.h>
#include <signal.h>

void timeout(int sig)
{
    if (sig == SIGALRM)
        puts("Time out!");
    alarm(2);
}

int main(int argc, char *argv[])
{
    int i;
    struct sigaction act;
    act.sa_handler = timeout;    //保存函数指针
    sigemptyset(&act.sa_mask);   //将 sa_mask 函数的所有位初始化成0
    act.sa_flags = 0;            //sa_flags 同样初始化成 0
    sigaction(SIGALRM, &act, 0); //注册 SIGALRM 信号的处理器。

    alarm(2); //2 秒后发生 SIGALRM 信号

    for (int i = 0; i < 3; i++)
    {
        puts("wait...");
        sleep(100);
    }
    return 0;
}
```

**利用信号处理技术消灭僵尸进程**

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <signal.h>
#include <sys/wait.h>

void read_childproc(int sig)
{
    int status;
    pid_t id = waitpid(-1, &status, WNOHANG);
    if (WIFEXITED(status))
    {
        printf("Removed proc id: %d \n", id);             //子进程的 pid
        printf("Child send: %d \n", WEXITSTATUS(status)); //子进程的返回值
    }
}

int main(int argc, char *argv[])
{
    pid_t pid;
    struct sigaction act;
    act.sa_handler = read_childproc;
    sigemptyset(&act.sa_mask);
    act.sa_flags = 0;
    sigaction(SIGCHLD, &act, 0);

    pid = fork();
    if (pid == 0) //子进程执行阶段
    {
        puts("Hi I'm child process");
        sleep(10);
        return 12;
    }
    else //父进程执行阶段
    {
        printf("Child proc id: %d\n", pid);
        pid = fork();
        if (pid == 0)
        {
            puts("Hi! I'm child process");
            sleep(10);
            exit(24);
        }
        else
        {
            int i;
            printf("Child proc id: %d \n", pid);
            for (i = 0; i < 5; i++)
            {
                puts("wait");
                sleep(5);
            }
        }
    }
    return 0;
}
```

### 10.4 基于多任务的并发服务器

**服务器端代码**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <signal.h>
#include <sys/wait.h>
#include <arpa/inet.h>
#include <sys/socket.h>

#define BUF_SIZE 30
void error_handling(char *message);
void read_childproc(int sig);

int main(int argc, char *argv[])
{
    int serv_sock, clnt_sock;
    struct sockaddr_in serv_adr, clnt_adr;

    pid_t pid;
    struct sigaction act;
    socklen_t adr_sz;
    int str_len, state;
    char buf[BUF_SIZE];
    if (argc != 2)
    {
        printf("Usgae : %s <port>\n", argv[0]);
        exit(1);
    }
    act.sa_handler = read_childproc; //防止僵尸进程
    sigemptyset(&act.sa_mask);
    act.sa_flags = 0;
    state = sigaction(SIGCHLD, &act, 0);         //注册信号处理器,把成功的返回值给 state
    serv_sock = socket(PF_INET, SOCK_STREAM, 0); //创建服务端套接字
    memset(&serv_adr, 0, sizeof(serv_adr));
    serv_adr.sin_family = AF_INET;
    serv_adr.sin_addr.s_addr = htonl(INADDR_ANY);
    serv_adr.sin_port = htons(atoi(argv[1]));

    if (bind(serv_sock, (struct sockaddr *)&serv_adr, sizeof(serv_adr)) == -1) //分配IP地址和端口号
        error_handling("bind() error");
    if (listen(serv_sock, 5) == -1) //进入等待连接请求状态
        error_handling("listen() error");

    while (1)
    {
        adr_sz = sizeof(clnt_adr);
        clnt_sock = accept(serv_sock, (struct sockaddr *)&clnt_adr, &adr_sz);
        if (clnt_sock == -1)
            continue;
        else
            puts("new client connected...");
        pid = fork(); //此时，父子进程分别带有一个套接字
        if (pid == -1)
        {
            close(clnt_sock);
            continue;
        }
        if (pid == 0) //子进程运行区域,此部分向客户端提供回声服务
        {
            close(serv_sock); //关闭服务器套接字，因为从父进程传递到了子进程
            while ((str_len = read(clnt_sock, buf, BUFSIZ)) != 0)
                write(clnt_sock, buf, str_len);

            close(clnt_sock);
            puts("client disconnected...");
            return 0;
        }
        else
            close(clnt_sock); //通过 accept 函数创建的套接字文件描述符已经复制给子进程，因为服务器端要销毁自己拥有的
    }
    close(serv_sock);

    return 0;
}

void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
void read_childproc(int sig)
{
    pid_t pid;
    int status;
    pid = waitpid(-1, &status, WNOHANG);
    printf("removed proc id: %d \n", pid);
}
```

调用 fork 函数时复制父进程的所有资源，但是套接字不是归进程所有的，而是归操作系统所有，只是进程拥有代表相应套接字的文件描述符。

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed//202312011550650.png)

如图所示，1 个套接字存在 2 个文件描述符时，只有 2 个文件描述符都终止（销毁）后，才能销毁套接字。如果维持图中的状态，即使子进程销毁了与客户端连接的套接字文件描述符，也无法销毁套接字（服务器套接字同样如此）。因此调用 fork 函数时，要将无关紧要的套接字文件描述符关掉。

### 10.5 分割 TCP 的 I/O 程序

客户端的父进程负责接收数据，额外创建的子进程负责发送数据，分割后，不同进程分别负责输入输出，这样，无论客户端是否从服务器端接收完数据都可以进程传输。

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <sys/socket.h>

#define BUF_SIZE 30
void error_handling(char *message);
void read_routine(int sock, char *buf);
void write_routine(int sock, char *buf);

int main(int argc, char *argv[])
{
    int sock;
    pid_t pid;
    char buf[BUF_SIZE];
    struct sockaddr_in serv_adr;
    if (argc != 3)
    {
        printf("Usage : %s <IP> <port>\n", argv[0]);
        exit(1);
    }
    sock = socket(PF_INET, SOCK_STREAM, 0);
    memset(&serv_adr, 0, sizeof(serv_adr));
    serv_adr.sin_family = AF_INET;
    serv_adr.sin_addr.s_addr = inet_addr(argv[1]);
    serv_adr.sin_port = htons(atoi(argv[2]));

    if (connect(sock, (struct sockaddr *)&serv_adr, sizeof(serv_adr)) == -1)
        error_handling("connect() error!");

    pid = fork();
    if (pid == 0)
        write_routine(sock, buf);
    else
        read_routine(sock, buf);

    close(sock);
    return 0;
}

void read_routine(int sock, char *buf)
{
    while (1)
    {
        int str_len = read(sock, buf, BUF_SIZE);
        if (str_len == 0)
            return;

        buf[str_len] = 0;
        printf("Message from server: %s", buf);
    }
}
void write_routine(int sock, char *buf)
{
    while (1)
    {
        fgets(buf, BUF_SIZE, stdin);
        if (!strcmp(buf, "q\n") || !strcmp(buf, "Q\n"))
        {
            shutdown(sock, SHUT_WR); //向服务器端传递 EOF,因为fork函数复制了文件描述度，所以通过1次close调用不够
            return;
        }
        write(sock, buf, strlen(buf));
    }
}

void error_handling(char *message)
{
    fputs(message, stderr);
    fputc('\n', stderr);
    exit(1);
}
```

## 第 11 章 进程间通信

### 11.1 进程间通信的基本概念

管道并非属于进程的资源，而是和套接字一样，属于操作系统（也就不是 fork 函数的复制对象）。所以，两个进程通过操作系统提供的内存空间进行通 信。






















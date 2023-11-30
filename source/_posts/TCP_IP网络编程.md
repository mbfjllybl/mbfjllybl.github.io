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

底层 I/O 函数与 ANSI 标准定义的文件 I/O 函数有何区别？

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

下面那些是面向消息的套接字的特性？ 

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












































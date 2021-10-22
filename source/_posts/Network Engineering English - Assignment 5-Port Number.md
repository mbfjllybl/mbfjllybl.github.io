---
title: Network Engineering English - Assignment 5-Port Number
date: 2021-10-22 10:28:31
updated:
tags: Computer Network
categories: Computer Translate
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

# Network Engineering English - Assignment 5-Port Number

Port Number（端口号）

In computer networking, a port is an application-specific or process-specific software construct serving as a communications endpoint.

> 在计算机网络中，端口就是作为会话端点的应用程序特定的或进程特定的软件概念。 

It is used by Transport Layer protocols of the Internet Protocol Suite, such as Transmission Control Protocol (TCP) and User Datagram Protocol (UDP).

> 端口被用于互联网协议集中的传输层协议，就像传输控制协议（TCP）和用户数据报协议（UDP）。

A specific port is identified by its number, commonly known as the port number, the IP address with which it is associated, and the protocol used for communication.

> 一个特定的端口是通过端口号来确定的，众所周知叫做端口号，IP地址与其关联，并且和使用的协议进行通信。

A well-known range of port numbers is reserved by convention to identify specific service types on hosts.

> 一系列众所周知的端口号因为在主机上确定特定的服务类型而被习俗性的保存起来。

In the client-server model of application architecture this is used to provide a multiplexing service on each port number that network clients connect to for service initiation, after which communication is reestablished on other connection-specific port numbers.

> 应用程序体系结构中的客户-服务器模型用来在每个网络客户端为了服务开始而连接的端口号提供多路复用的服务，之后会话被重新建立在其它特定连接的端口号上。 

Technical details（技术细节）

Transport Layer protocols, such as the Transmission Control Protocol (TCP), the User Datagram Protocol (UDP), specify a source and destination port number in their packet headers.

> 传输层协议，诸如传输控制协议（TCP），用户数据报协议（UDP）， 会在它们的头部指定一个资源和目的地的端口号。

A port number is a 16-bit unsigned integer, thus ranging from 0 to 65535.

> 端口号是16位的无符号整数，因此从0排列到65535.

A process associates its input or output channel file descriptors (sockets) with a port number and an IP address, a process known as binding, to send and receive data via the network.

> 进程会与它输入或输出频道文件的描述符（套接口）联系在一个端口号，IP地址，已知的捆绑的进程上，从而通过网络发送和接受数据。

The operating system's networking software has the task of transmitting outgoing data from all application ports onto the network, and forwarding arriving network packets to a process by matching the packets IP address and port numbers.

> 操作系统的网络软件有从在网络上的应用程序端口传出数据和通过匹配数据包的IP地址和端口号转发正在到达的数据包到一个进程的任务。

Applications implementing common services often use specifically reserved, well-known port numbers for receiving service requests from client hosts.

> 应用程序执行的一般的服务常常使用特殊保留的，众所周知的端口号来从客户端上接受服务请求。

This process is known as listening and involves the receipt of a request on the well-known port and reestablishing one-to-one server-client communications on another private port, so that other clients may also contact the well-known service port.

> 这个进程被认为是在众所周知的端口上监听和涉及接受请求并且在另一个私人接口上重新建立一对一的服务器-客户端的会话，以至于其他客户端也可能在这个众所周知的端口上进行连接。

The well-known ports are defined by convention overseen by the Internet Assigned Numbers Authority (IANA), cf. list of TCP and UDP port numbers.

> 这些众所周知的端口是通过互联网地址编码分配机构（IANA）公约监督，参照TCP和UDP端口号的列表而确定的。

The core network services, such as the World-Wide Web, typically use small port numbers, less than 1024.

> 核心的网络服务，诸如万维网就是典型使用小于1024的小端口号。

In many Unix-like operating systems superuser privileges are required for creation of these ports, since these are often deemed critical to the operation of IP networks.

> 在许多的传统的多用户的计算机操作系统超级用户特权创建这些端口，因为这些通常被视为关键的IP网络操作。

Conversely, the client end of a connection typically uses a high port number allocated for short term use, therefore called an ephemeral port.

> 相反的，典型地客户端连接使用一个为短期使用的分配的高位的端口号，因此被叫做暂时性的端口。

Port numbers are encoded in the transport protocol packet header, and they can be readily interpreted not only by the sending and receiving computers, but also by other components of the networking infrastructure.

> 端口号被编码在传输协议数据包的头部，而且它们可以很容易的被解读不仅仅是被发送和接受的计算机，还有其它网络基本设施的组件。

In particular, firewalls are commonly configured to differentiate between packets depending on their source or destination port numbers.

> 特别的是，防火墙一般都被设置在取决于它们资源的数据包之间或目的地端口号之间。 

Port forwarding is an example application of this.

> 端口转发是这类程序的一个例子。

Processes create associations with transport protocol ports by means of sockets.

> 进程创造了与通过套接口的传输协议端口的联系。

A socket is the software structure used as the transport end-point.

> 套接口就是用于传输端节点的软件结构。

It is created by the operating system for the process and bound to a socket address which consists of a combination of a port number and an IP address.

> 套接口是通过用于进程的操作系统和绑定于一个包含端口号和IP地址的结合的套接口地址。 

Sockets may be set to send or receive data in one direction at a time (half duplex) or simultaneously in both directions (full duplex).

> 套接口可能是被设置用于一次性单方向（半双工）或同时发生在两个方向（全双工）的发送或接收数据。

Because different services commonly listen on different port numbers, the practice of attempting to connect to a range of ports in sequence on a single computer is commonly known as port scanning.

> 因为不同服务一般都监听不同的端口号，试图在一台计算机上依次连接一系列端口的练习一般被称为端口扫描技术。

This is usually associated either with malicious cracking attempts or with network administrators looking for possible vulnerabilities to help prevent such attacks.

> 这通常与恶意开裂的尝试或网络管理员为帮助阻止类似的攻击寻找可能的缺陷。 

Port connection attempts are frequently monitored and logged by computers.

> 端口连接尝试是被经常监控和计算机所记录的。

The technique of port knocking uses a series of port connections (knocks) from a client computer to enable a server connection.

> 端口碰撞技术使用一系列来自客户端计算机的端口连接（碰撞）来启用一个服务器连接。 

Examples（示例）

An example for the use of ports is the Internet mail system.

> 使用端口的一个实例就是互联网邮件系统。

A server used for sending and receiving email generally needs two services.

> 用于发送和接受邮件的服务器大体上需要两个服务。

The first service is used to transport email to and from other servers.

> 第一个服务用来传送邮件到其它服务器或接收从其它服务器来的邮件。

This is accomplished with the Simple Mail Transfer Protocol (SMTP).

> 这一步骤的完成使用了简单邮件传输协议（SMTP）。

The SMTP service application usually listens on TCP port 25 for incoming requests.

> SMTP应用程序服务通常监听TCP端口25来接受请求。

The second service is the Post Office Protocol (POP) which is used by e-mail client applications on user's personal computers to fetch email messages from the server.

> 第二个服务就是用于位于用户的个人电脑用于从服务器接收信息的电子邮件客户端应用程序邮局协议（POP）。

The POP service listens on TCP port number 110.

> POP服务监听TCP端口100.

Both services may be running on the same host computer, in which case the port number distinguishes the service that was requested by a remote computer, be it a user's computer or another mail server.

> 这两个服务可能在同一个主机计算机运行，在这种情况下端口号区分通过远程计算机请求的服务，是一个用户的计算机或另一个邮件服务器。

While the listening port number of a server is well defined (IANA calls these the well known ports), the client's port number is often chosen from the dynamic port range (see below).

> 当监听服务器的端口号很明确时（IANA把这叫做常用端口），客户端的端口号经常从动态的端口范围（从低到高）。

In some applications, the client and the server each use specific port numbers assigned by the IANA.

> 在一些应用程序中，客户端和服务器都使用IANA指定的特定的端口号。

A good example of this is DHCP in which the client always uses UDP port 68 and the server always uses UDP port 67.

> 这种情况的一个例子是DHCP，在其中客户端常常使用UDP端口68，服务器常常使用UDP端口67。

Common port numbers（常用的端口号）

The Internet Assigned Numbers Authority (IANA) is responsible for the global coordination of the DNS Root, IP addressing, and other Internet protocol resources.

> 互联网地址编码分配机构（IANA）负责了全球的DNS根，IP地址和其它网络协议资源的协调。

This includes the registration of commonly used port numbers for well-known Internet services. 

> 包括了众所周知的网络服务一般使用的端口号的注册。

The port numbers are divided into three ranges: the well-known ports, the registered ports, and the dynamic or private ports.

> 端口号被分成三个范围：众所周知的端口，注册的端口和动态的或私有的端口。 

The well-known ports are those from 0 through 1023. Examples include:

> 这些从0到1023中众所周知的端口，示例如下：

21: FTP（文件传输协议）

23: Telnet（以太网）

53: Domain Name System（域名解析系统）

80: World Wide Web HTTP（万维网超文本传输协议）

119: Network News Transfer Protocol（网络新闻传输协议）

443: HTTP over Transport Layer Security/Secure Sockets Layer（传输安全层/安全套接字层超文本传输协议）

445: microsoft-ds, Server Message Block over TCP（端口，TCP服务器消息块）

The registered ports are those from 1024 through 49151.

> 被注册的端口是那些从1024到49151的。

A list of registered ports may be found on the IANA Website.

> 在IANA网站可能会找到一系列的注册端口。

The dynamic or private ports are those from 49152 through 65535 (see ephemeral port). 

> 动态的或私有的端口是从49152到65535的（参照临时端口）。
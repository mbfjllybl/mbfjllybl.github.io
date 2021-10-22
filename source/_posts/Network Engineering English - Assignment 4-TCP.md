---
title: Network Engineering English - Assignment 4-TCP
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

# Network Engineering English - Assignment 4-TCP

Transmission Control Protocol（传输控制协议） 

The Transmission Control Protocol (TCP) is one of the core protocols of the Internet Protocol Suite.

> 传输控制协议是互联网协议集中的核心成员之一。

TCP is one of the two original components of the suite (the other being Internet Protocol, or IP), so the entire suite is commonly referred to as TCP/IP.

> TCP协议是两个原始组件套件（另一个是IP协议）之一，这整个套件通常被称为TCP/IP。

Whereas IP handles lower-level transmissions from computer to computer as a message makes its way across the Internet, TCP operates at a higher level, concerned only with the two end systems, for example a Web browser and a Web server.

> 然而IP协议处理低等级的从计算机到计算机的传输就像消息通过互联网前进，TCP协议操作一个更高的水平，只关注两个终端系统，例如一个Web浏览器和一个Web服务器。

In particular, TCP provides reliable, ordered delivery of a stream of bytes from a program on one computer to another program on another computer.

> 特别的是，TCP协议提供从一个计算机上的程序到另一个计算机的程序的可靠地，有序的字节流传输。

Besides the Web, other common applications of TCP include e-mail and file transfer. 

> 除了Web之外，还有其他常见的应用TCP协议的应用程序，例如电子邮件和文件传输。

Among its other management tasks, TCP controls segment size, the rate at which data is exchanged, and network traffic congestion.

> 在它的其它管理的任务中，TCP协议控制段的大小，数据的交换率和网络交通拥塞。

Historical origin（历史起源）

In May, 1974, the Institute of Electrical and Electronic Engineers (IEEE) published a paper entitled "A Protocol for Packet Network Interconnection."

> 1974年5月， 电气与电子工程师协会（IEEE）发表了一篇名为“数据报网络互联协议”的文章。

The paper's authors, Vint Cerf and Bob Kahn, described an internetworking protocol for sharing resources using packet-switching among the nodes.

> 这篇文章的作者Vint Cerf和Bob Kahn讲述了一个为在节点之间使用分组交换分享资源而存在的协议。

A central control component of this model was the Transmission Control Program that incorporated both connection-oriented links and datagram services between hosts.

> 该模型的核心组件是将面向连接的链接和主机之间的数据报服务结合在一起的传输控制程序。

The monolithic Transmission Control Program was later divided into a modular architecture consisting of the Transmission Control Protocol at the connection-oriented layer and the Internet Protocol at the internetworking (datagram) layer.

> 庞大的传输控制程序不久就被分成包含用于面向连接的层次的传输控制协议和用于网络互连（数据报）层次的互联网协议的模块结构。

The model became known informally as TCP/IP, although formally it was henceforth called the Internet Protocol Suite.

> 这个模型随后被广为人知并被非正式的叫做TCP/IP，从那以后它被正式的命名为互联网协议集。

Network function（网络功能）

TCP provides a communication service at an intermediate level between an application program and the Internet Protocol (IP).

> TCP协议提供在一个应用程序和互联网协议之间的中间层的交流服务。

That is, when an application program desires to send a large chunk of data across the Internet using IP, instead of breaking the data into IP-sized pieces and issuing a series of IP requests, the software can issue a single request to TCP and let TCP handle the IP details.

> 也就是当一个应用程序想通过使用IP协议的网络传输大量数据的时候，不同于把数据分成适合IP协议大小的几份并发出一系列的IP请求，它的软件会发出一个单一的请求给TCP然后TCP协议处理IP数据。

IP works by exchanging pieces of information called packets.

> IP会通过交换信息的片段也叫数据包进行工作。

A packet is a sequence of bytes and consists of a header followed by a body.

> 数据包就是一个序列字节并包含在跟随在整个数据的头部。

The header describes the packet's destination and, optionally, the routers to use for forwarding until it arrives at its final destination.

> 头部用来描述数据包的目的地并且可选的，使用路由器指向直到数据包到达它最终的目的地。

The body contains the data which IP is transmitting.

> 整体包含了IP正在传输的数据。

Due to network congestion, traffic load balancing, or other unpredictable network behavior, IP packets can be lost or delivered out of order.

> 由于网络的拥塞，交通负载的平衡或是不可预知的网络表现，IP数据包可能会丢失或是无序的交付。

TCP detects these problems, requests retransmission of lost packets, rearranges out-of-order packets, and even helps minimize network congestion to reduce the occurrence of the other problems.

> TCP会查出这些问题，请求重传丢失的数据包，重排无序的数据包， 甚至是最小化网络的拥塞来减少其他问题的发生。

Once the TCP receiver has finally reassembled a perfect copy of the data originally transmitted, it passes that datagram to the application program.

> 一旦TCP接收方最终完成被重新召集的完美的数据传输原始版本的复制，它就会发送数据报到应用程序。

Thus, TCP abstracts the application's communication from the underlying networking details. 

> 因此，TCP会从潜在的网络数据提取应用程序的对话。

TCP is used extensively by many of the Internet's most popular applications, including the World Wide Web, E-mail, File Transfer Protocol, Secure Shell, and some streaming media applications. 

> TCP被广泛用于许多网络最流行的应用程序，包括万维网，电子邮件，文件传输协议，安全外壳协议和一些流媒体应用程序。

TCP is optimized for accurate delivery rather than timely delivery, and therefore, TCP sometimes incurs relatively long delays (in the order of seconds) while waiting for out-of-order messages or retransmissions of lost messages.

> TCP优化了准确的交付多于按时的交付，因此，TCP有时会导致相当长的延迟（以秒计算）来等待无序的信息或是丢失数据的重传。

It is not particularly suitable for real-time applications such as Voice over IP.

> 所以TCP不是特别适合类似IP电话这样的实时应用程序。

For such applications, protocols like the Real-time Transport Protocol (RTP) running over the User Datagram Protocol (UDP) are usually recommended instead.

> 对于这样的实时应用程序，像实时传输协议（RTP）运行在用户数据包传输协议（UDP）这样的协议通常会被推荐代替TCP。

TCP is a reliable stream delivery service that guarantees delivery of a data stream sent from one host to another without duplication or losing data.

> TCP是一个会保证从一个主机发送到另一个主机的数据流不会多发或丢失数据的可靠地数据流交付服务。

Since packet transfer is not reliable, a technique known as positive acknowledgment with retransmission is used to guarantee reliability of packet transfers.

> 由于数据包传输是不可靠的，一个被称为重传肯定确认的技术就被用来保证数据包传输的可靠性。

This fundamental technique requires the receiver to respond with an acknowledgment message as it receives the data.

> 这种基础的技术要求接受方在接收数据后回应一个用于确认的信息。

The sender keeps a record of each packet it sends, and waits for acknowledgment before sending the next packet.

> 发送方每发送一个数据包都会保存一个记录，并且在发送下一个数据包之前等待确认信息。 

The sender also keeps a timer from when the packet was sent, and retransmits a packet if the timer expires.

> 发送方也会在发送数据包时启动一个计时器，如果计时器超过期限就会重发这个数据包。 

The timer is needed in case a packet gets lost or corrupted.

> 万一数据包丢失或损坏也会需要计时器。

TCP consists of a set of rules: for the protocol, that are used with the Internet Protocol, and for the IP, to send data "in a form of message units" between computers over the Internet.

> TCP包含了一系列的规则：对于协议，在网络上的计算机之间发送“一个单位的信息单元”的数据使用互联网协议和IP。

At the same time that IP takes care of handling the actual delivery of the data, TCP takes care of keeping track of the individual units of data transmission, called segments, that a message is divided into for efficient routing through the network.

> 同时IP协议处理实际的数据交付，TCP保持着数据传输中个别单元，也就是为了通过网络的高效路由选择将整个信息分割成的，片段的路径。

For example, when an HTML file is sent from a Web server, the TCP software layer of that server divides the sequence of bytes of the file into segments and forwards them individually to the IP software layer (Internet Layer).

> 例如，当一个Web服务器发送一个HTML文件时，TCP服务器的软件层会将整个序列的字节分割成片段并分别把它们发送到IP软件层（网络层）。

The Internet Layer encapsulates each TCP segment into an IP packet by adding a header which includes (among other data) the destination IP address.

> 网络层通过添加一个包括（在其他数据之中）目的地IP地址的头把每个TCP片段封装成IP数据包。

Even though every packet has the same destination address, they can be routed on different paths through the network.

> 即使每一个数据包都有同样的目的地地址，它们也会经过网络中不同的路径。

When the client program on the destination computer receives them, the TCP layer (Transport Layer) reassembles the individual segments and ensures they are correctly ordered and error-free as it streams them to an application.

> 当一个目标计算机客户端程序接收到数据包的时候，TCP层（传输层）会重新组装每一个片段并在它们流入应用程序是确定它们是正确的顺序和无误的。
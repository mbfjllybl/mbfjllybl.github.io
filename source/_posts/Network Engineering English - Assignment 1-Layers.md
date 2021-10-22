---
title: Network Engineering English - Assignment 1-Layers
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

# Network Engineering English - Assignment 1-Layers

Layers in the TCP/IP Model

The layers near the top are logically closer to the user application, while those near the bottom are logically closer to the physical transmission of the data.

> TCP/IP中顶端的层次在逻辑上与用户的应用程序联系非常紧密，而低端的层次逻辑上与数据的物理传输有紧密的关系。

Viewing layers as providing or consuming a service is a method of abstraction to isolate upper layer protocols from the detail of transmitting bits over, for example, Ethernet and collision detection, while the lower layers avoid having to know the details of each and every application and its protocol.

> 当低层次拒绝获得每个用户应用程序与其协议时，可以把层次关系当做提供或消耗一项服务是抽象隔离从传输数据的细节中体现的较高的层次协议的方法，例如，以太网和冲突检测。

This abstraction also allows upper layers to provide services that the lower layers cannot, or choose not to, provide.

> 这种做法同样可以使较高的层次提供较低层次不能或选择不能提供的服务。

Again, the original OSI Reference Model was extended to include connectionless services. 

> 除此之外，新颖的开放式系统互连参考模型可以被扩展到包括无连接的服务。 

For example, IP is not designed to be reliable and is a best effort delivery protocol. 

> 比如，IP协议没有被设计成值得依赖的协议，但它是最有成就的的传送协议。

This means that all transport layer implementations must choose whether or not to provide reliability and to what degree.

> 这意味着所有的运输层次的实现都必须选择是否提供可靠性和所能提供的程度。

UDP provides data integrity (via a checksum) but does not guarantee delivery; TCP provides both data integrity and delivery guarantee (by retransmitting until the receiver acknowledges the reception of the packet).

> UDP协议保证数据的完整性（通过一个校验码）但并不保证是否传送；TCP协议既能提供数据的完整性又能保证数据的发送（通过中继直到接收方确定收到接受的数据包）。

Link Layer（链路层）

The Link Layer is the networking scope of the local network connection to which a host is attached.

> 链路层是指本地网络到每一台被连接主机的网络范围。

This regime is called the link in Internet literature.

> 这种制度在互联网术语中称作连接。

This is the lowest component layer of the Internet protocols, as TCP/IP is designed to be hardware independent.

> 它在网络协议中是最低级的层次成分，而TCP/IP协议是与硬件无关的。

As a result TCP/IP has been implemented on top of virtually any hardware networking technology in existence.

> 结果TCP/IP事实上已经被实现在现有任何硬件网络技术上了。

The Link Layer is used to move packets between the Internet Layer interfaces of two different hosts on the same link.

> 链路层被用来在网络层两台同样连接的不同主机的接口之间移动数据包。

The processes of transmitting and receiving packets on a given link can be controlled in the software device driver for the network card, as well as on firmware or specialized chipsets.

> 一个被给出的传输和接受数据包的进程可以被控制在软件设备的网卡驱动程序、固化在硬件上的软件和专门的芯片组中。

These will perform data link functions such as adding a packet header to prepare it for transmission, and then actually transmit the frame over a physical medium.

> 这些都可以执行数据连接功能，例如，添加一个数据包包头为传输做准备，然后确切的通过物理介质传输该框架。

The TCP/IP model includes specifications of translating the network addressing methods used in the Internet Protocol to data link addressing, such as Media Access Control (MAC), however all other aspects below that level are implicitly assumed to exist in the Link Layer, but are not explicitly defined.

> TCP/IP模型包括用于数据连接寻址的网络协议中的翻译网络寻址的方法的详细参数，诸如媒体存取控制（MAC），然而所有低于那个水平的方面默认假定存在于链路层，但是没有被明确的定义。

The Link Layer is also the layer where packets may be selected to be sent over a virtual private network or other networking tunnel.

> 链路层也是数据包可能会选择通过虚拟私人网络或其他网络渠道发送到的层次

In this scenario, the Link Layer data may be considered application data which traverses another instantiation of the IP stack for transmission or reception over another IP connection.

> 在这种情况下，链路层数据可能被认为是穿过另一个IP堆栈传送或接受另一个IP连接的实例化应用程序数据。

Such a connection, or virtual link, may be established with a transport protocol or even an application scope protocol that serves as a tunnel in the Link Layer of the protocol stack.

> 这样的一个连接，或是虚拟连接可能是用一种传输协议甚至是一个像是协议堆中的一个链路层的渠道应用程序范围协议建立的。

Thus, the TCP/IP model does not dictate a strict hierarchical encapsulation sequence.

> 因此，TCP/IP模型没有指出一个等级严格的封装顺序。

Internet Layer（网络层）

The Internet Layer solves the problem of sending packets across one or more networks. 

> 网络层解决了从一个网络发送数据包到更多网络的难题。

Internetworking requires sending data from the source network to the destination network. 

> 互联网络要求从源网络发送数据到目标网络。

This process is called routing.

> 这个进程叫做路由选择。

In the Internet Protocol Suite, the Internet Protocol performs two basic functions:

> 在一套网络协议中，网络协议表现出两个基本的功能：

Host addressing and identification: This is accomplished with a hierarchical addressingsystem (see IP address).

> 主机定址和鉴别：这个功能通过一个分等级的寻址系统完成（像IP地址那样）。

Packet routing: This is the basic task of getting packets of data (datagrams) from source to

destination by sending them to the next network node (router) closer to the final destination.

> 数据包的路由选择：这是通过发送包到下一个接近最终目的地的网络结点（路由器）从源位置到目的地址取数据包（数据报）的基本任务。

IP can carry data for a number of different upper layer protocols.

> IP可以为大量不同的较高层次协议携带数据。

These protocols are each identified by a unique protocol number: for example, Internet Control Message Protocol (ICMP) and Internet Group Management Protocol (IGMP) are protocols 1 and 2, respectively.

> 这些协议都通过一个独一无二的协议号鉴别：例如，网间控制报文协议（ICMP）和因特网组管理协议（IGMP）分别是协议1，协议2。

Some of the protocols carried by IP, such as ICMP (used to transmit diagnostic information about IP transmission) and IGMP (used to manage IP Multicast data) are layered on top of IP but perform internetworking functions.

> 有一些协议通过IP携带，诸如ICMP（用来传输关于IP传输诊断程序信息）和IGMP（用来管理IP多路传送数据）是在IP顶端分层的但是表现出互联网络的功能。

This illustrates the differences in the architecture of the TCP/IP stack of the Internet and the OSI model.

> 以上说明TCP/IP网络堆栈和OSI模型体系结构上的不同。

Transport Layer（传输层）

The Transport Layer's responsibilities include end-to-end message transfer capabilities independent of the underlying network, along with error control, segmentation, flow control, congestion control, and application addressing (port numbers).

> 传输层的责任包括端对端信息传送能力基本网络的独立性，差错控制，分割，流量控制，拥塞控制，和应用程序定址（端口号）。

End to end message transmission or connecting applications at the transport layer can be categorized as either connection-oriented, implemented in Transmission Control Protocol (TCP), or connectionless, implemented in User Datagram Protocol (UDP).

> 端对端信息传输或在传输层连接应用程序可以分类成面向连接的，应用在传输控制协议（TCP），或者是无连接的，应用在用户数据报协议（UDP）。

The Transport Layer can be thought of as a transport mechanism, e.g., a vehicle with the responsibility to make sure that its contents (passengers/goods) reach their destination safely and soundly, unless another protocol layer is responsible for safe delivery.

> 传输层可以被看做一个传输机制，例如，一辆车的责任就是确保它的乘客安全稳健的到达目的地，除此之外，另一个协议层的责任是安全的交付。

The Transport Layer provides this service of connecting applications through the use of service ports.

> 传输层提供通过使用服务端口连接应用程序的服务。

Since IP provides only a best effort delivery, the Transport Layer is the first layer of the TCP/IP stack to offer reliability.

> 自从IP协议只提供一种最大努力地交付，传输层是第一个TCP/IP中提供保障性的层次。 

IP can run over a reliable data link protocol such as the High-Level Data Link Control (HDLC). 

> IP协议能运行可依赖的数据连接诸如高级数据链路控制（HDLC）。

Protocols above transport, such as RPC, also can provide reliability.

> 用于传输的协议，例如RPC（位置遥控），也能提供可依赖性服务。

For example, the Transmission Control Protocol (TCP) is a connection-oriented protocol that addresses numerous reliability issues to provide a reliable byte stream:

> 例如，传输控制协议（TCP）是一个面向连接寻找众多可依赖性问题提供一个可依赖的字节流的协议。

data arrives in-order （数据按次序到达）

data has minimal error （数据最少的错误）

duplicate data is discarded （重复的数据会被丢弃）

lost/discarded packets are resent （不允许丢失/丢弃的数据包）

includes traffic congestion control （包括交通拥塞控制）



User Datagram Protocol is a connectionless datagram protocol.

> 用户数据电报协议是无连接的数据报协议。

Like IP, it is a best effort, "unreliable" protocol.

> 像IP协议，它是尽最大努力的“不可靠”协议。

Reliability is addressed through error detection using a weak checksum algorithm.

> 可靠性是用不牢靠的校验码运算法则检测错误的寻址的。

UDP is typically used for applications such as streaming media (audio, video, Voice over IP etc) where on-time arrival is more important than reliability, or for simple query/response applications like DNS lookups, where the overhead of setting up a reliable connection is disproportionately large.

> UDP协议是用于类似于流媒体（音频，视频，网络电话等等）这种重视实时传达性多于可靠性或是用于简单的访问/回应像可获取邮件交换机信息系统这种建立可靠连接的表层应用不成比例的大的应用程序的应用程序代表性协议。

Real-time Transport Protocol (RTP) is a datagram protocol that is designed for real-time data such as streaming audio and video.

> 实时传输协议（RTP）是一种被设计用于流音频和流视频类型的实时数据传输数据报协议。 

TCP and UDP are used to carry an assortment of higher-level applications.

> TCP协议和UDP协议用于传送一套高水平应用的。

The appropriate transport protocol is chosen based on the higher-layer protocol application. 

> 选择适当的传输协议是基于高层次协议应用程序的。

For example, the File Transfer Protocol expects a reliable connection, but the Network File System (NFS) assumes that the subordinate Remote Procedure Call protocol, not transport, will guarantee reliable transfer.

> 例如，文件传输协议需要可靠的连接，网络文件系统承担下级的、非传输的远程过程协调协议会保证可靠地传输。

Other applications, such as VoIP, can tolerate some loss of packets, but not the reordering or delay that could be caused by retransmission.

> 其他的应用，像网络语音电话业务，可以忍受一些数据包的丢失，但不是中继引起的重排序或延迟。

The applications at any given network address are distinguished by their TCP or UDP port. 

> 通过他们TCP或UDP端口在给定网络地址的应用是很有效的。

By convention certain well known ports are associated with specific applications.

> 惯例确定的广为人知的端口是与具体的应用联系的。

Application Layer（应用层）

The Application Layer refers to the higher-level protocols used by most applications for network communication.

> 应用层指的是被大多数用于网络交流的应用的高水平协议。

Examples of application layer protocols include the File Transfer Protocol (FTP) and the Simple Mail Transfer Protocol (SMTP).

> 应用层协议有文件传输协议（FTP）和简单邮件传输协议（SMTP）。

Data coded according to application layer protocols are then encapsulated into one or (occasionally) more transport layer protocols (such as the Transmission Control Protocol (TCP) or User Datagram Protocol (UDP)), which in turn use lower layer protocols to effect actual data transfer.

> 根据应用层协议编码的数据接下来被压缩到一个或（偶尔的）多个用更低层次协议影响实际数据传输的传输层协议（例如传输控制协议（TCP）或用户数据报协议（UDP））。

Since the IP stack defines no layers between the application and transport layers, the application layer must include any protocols that act like the OSI's presentation and session layer protocols. 

> 自从IP堆栈定义应用层和传输层之间无其他层次后，应用层必须囊括任何类似于开放式互联参考模型的表达和会话层协议。

This is usually done through libraries.

> 这些通常通过函数库实现。

Application Layer protocols generally treat the transport layer (and lower) protocols as "black boxes" which provide a stable network connection across which to communicate, although the applications are usually aware of key qualities of the transport layer connection such as the end point IP addresses and port numbers.

> 虽然应用层常常意识到诸如端点IP地址和端口号传输层连接的关键品质，应用层协议大体上把传输层（或更低层）协议视为提供穿越交流的稳定网络连接的“黑匣子”。

As noted above, layers are not necessarily clearly defined in the Internet protocol suite. 

> 通过以上提到的，我们知道在一套网络协议中不必须在层次之间有明确的定义。

Application layer protocols are most often associated with client-server applications, and the commoner servers have specific ports assigned to them by the IANA: HTTP has port 80; Telnet has port 23; etc.

> 应用层协议通常与客户-服务器应用联系在一起，并且大众化的服务器都有被IANA指派的特殊端口：HTTP有端口80；以太网有端口23;等等。

Clients, on the other hand, tend to use ephemeral ports, i.e. port numbers assigned at random from a range set aside for the purpose.

> 换句话说，客户更倾向于使用临时的端口，也就是端口号是从为特殊目的设置的范围内随机分配的。

Transport and lower level layers are largely unconcerned with the specifics of application layer protocols.

> 传输层和更低水平的层次很大程度上不重视应用层协议的细节。

Routers and switches do not typically "look inside" the encapsulated traffic to see what kind of application protocol it represents, rather they just provide a conduit for it.

> 尽管他们只为它提供一个渠道，路由器和交换机通常并不“向里面看”封装的传输而是看它代表什么类型的应用协议。

However, some firewall and bandwidth throttling applications do try to determine what's inside, as with the Resource Reservation Protocol (RSVP).

> 然而，一些防火墙和带宽节流试图与资源决定协议(RSVP)决定内在.

It's also sometimes necessary for Network Address Translation (NAT) facilities to take account of the needs of particular application layer protocols.

> 网络地址转换（NAT）设备常常必须考虑到特殊应用层协议的需要。

(NAT allows hosts on private networks to communicate with the outside world via a single visible IP address using port forwarding, and is an almost ubiquitous feature of modern domestic broadband routers).

> （NAT允许私有网络上的主机通过一个可见的使用运输端口的IP地址与外界交流，并且这也是现代国内宽带路由器的非常普遍的特征。）
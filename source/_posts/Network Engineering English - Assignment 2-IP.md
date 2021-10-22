---
title: Network Engineering English - Assignment 2-IP
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

# Network Engineering English - Assignment 2-IP

Internet Protocol

The Internet Protocol (IP) is a protocol used for communicating data across a packet-switched internetwork using the Internet Protocol Suite.

> 互联网协议（IP）是一个使用互联网协议组来进行分组交换，从而数据通信的网络协议。 

IP is the primary protocol in the Internet Layer of the Internet Protocol Suite and has the task of delivering distinguished protocol datagrams (packets) from the source host to the destination host solely based on their addresses. 

> IP是互联网协议网络层的主要协议，并提供完全基于其地址的，将数据报（包）从源主机传输到目的主机的服务。

For this purpose the Internet Protocol defines addressing methods and structures for datagram encapsulation. The first major version of addressing structure, now referred to as Internet Protocol Version 4 (IPv4) is still the dominant protocol of the Internet, although the successor, Internet Protocol Version 6 (IPv6) is being deployed actively worldwide. 

> 为此，互联网协议定义了解决方法和数据报封装结构。第一个主要版本的结构，此结构现在被称为互联网协议版本4（IPv4），仍然是最主要的互联网协议，虽然其下一版本，Internet协议版本6（IPv6）正在全球积极部署。 

Services provided by IP

The Internet Protocol is responsible for addressing hosts and routing packets from a source host to the destination host across one or more IP networks. For this purpose the Internet Protocol defines an addressing system that has two functions. Addresses identify hosts and provide a logical location service. Each packet is tagged with a header that contains the meta-data for the purpose of delivery. This process of tagging is also called encapsulation. IP is a connectionless protocol and does not need circuit setup prior to transmission.

> 因特网协议负责处理位于一个或多个网络上来自源主机到目标主机的主机寻址和包的路径选择。为此，互联网协议定义了一个具有双功能的寻址系统。地址标识主机并提供逻辑位置服务。每一个数据包都带有一个包含元数据的头文件，用于传送的目的。这个标记的过程也被称为封装。IP协议是非面向连接的，不需要在传输电路设置。

Reliability

The design principles of the Internet protocols assume that the network infrastructure is inherently unreliable at any single network element or transmission medium and that it is dynamic in terms of availability of links and nodes. 

> 网络协议的设计原则是假定在任何单一的网络元素或传输介质中，网络基础设施本身是不可靠的，并且它在链接和节点中的可用性方面是动态化的。

No central monitoring or performance measurement facility exists that tracks or maintains the state of the network. For the benefit of reducing network complexity, the intelligence in the network is purposely mostly located in the end nodes of each data transmission, cf. end-to-end principle. Routers in the transmission path simply forward packets to the next known local gateway matching the routing prefix for the destination address. 

>跟踪和维护网络状态的中央监控设备和性能测量设备是不存在的。基于可以减少网络复杂性的优点，网络智能希望被用于端节点之间的数据传输，参照端到端原则。传输路径中的路由器只是转发数据包到下一个本地已知的匹配路由前缀的目的地址的网关。

As a consequence of this design, the Internet Protocol only provides best effort delivery and its service can also be characterized as unreliable. In network architectural language it is a connection-less protocol, in contrast to so-called connection-oriented modes of transmission. The lack of reliability allows any of the following fault events to occur:

> 由于这个设计，IP协议只是尽最大努力的交付并且这个服务也可以被描述为不可靠的。在网络构建语言中它是无连接的协议，而并不是所谓的面向连接的传输模式。可靠性的降低使得以下这些错误的发生：

data corruption (数据损毁)

lost data packets (数据包丢失)

duplicate arrival (重复发送)

out-of-order packet delivery; meaning, if packet 'A' is sent before packet 'B', packet 'B' may arrive before packet 'A'. Since routing is dynamic and there is no memory in the network about the path of prior packets, it is possible that the first packet sent takes a longer path to its destination.

> 无序的数据包交付；意为，假如A包先于B包发送，但B包先于A包到达。因为路由选择是动态的并且网络中没有关于路径中优先包的记忆，所以最先发送的数据包经由一个较长的路径到达目的地是有可能发生的。

The only assistance that the Internet Protocol provides in Version 4 (IPv4) is to ensure that the IP packet header is error-free through computation of a checksum at the routing nodes. This has the side-effect of discarding packets with bad headers on the spot. In this case no notification is required to be sent to either end node, although a facility exists in the Internet Control Message Protocol (ICMP) to do so.

> 唯一的辅助就是IP协议在IPv4中提供的通过计算路由选择的校验码确定IP数据包包头是无误的。这种方法的缺点是会丢弃节点中包头坏掉的数据包。在这种情况下尽管设备处于控制报文协议的控制，也不会有错误通知发送到另一个节点。

IPv6, on the other hand, has abandoned the use of IP header checksums for the benefit of rapid forwarding through routing elements in the network.

> 另一方面，IPv6摒弃了为达到迅速通过网络中路由元素进行传输目的的IP包头校验码的使用。

The resolution or correction of any of these reliability issues is the responsibility of an upper layer protocol. For example, to ensure in-order delivery the upper layer may have to cache data until it can be passed to the application.

> 解决或者说是纠正这些可靠性问题就成为了更高级协议的职责。例如，为了确定有序的数据交付更高级的协议可能必须缓存数据直到可以通过申请。

In addition to issues of reliability, this dynamic nature and the diversity of the Internet and its components provide no guarantee that any particular path is actually capable of, or suitable for performing the data transmission requested, even if the path is available and reliable. One of nontechnical constraints is the size of data packets allowed on a given link. An application must assure that it uses proper transmission characteristics. Some of this responsibility lies also in the upper layer protocols between application and IP.

> 除了可靠性问题，动态性、互联网的差异性和它的组件不能保证任何特定路径是有能力的，或适合数据传输要求的表现，甚至是路径是有效地和可靠地。技术约束条件之一是在一个给定的连接上允许传送的数据包的大小。应用程序必须弄清楚它所使用的合适的传输的特性。一些这样的职责也来自于应用程序和IP协议之间更高层次的协议。

Facilities exist to examine the maximum transmission unit (MTU) size of the local link, as well as for the entire projected path to the destination when using IPv6. The IPv4 internetworking layer has the capability to automatically fragment the original datagram into smaller units for transmission. In this case, IP does provide re-ordering of fragments delivered out-of-order.

> 设备的存在是为了检查本地连接的最大传输单元（MTU）的大小，而且也是为了使用IPv6时整个预计的到达目的地的路径。IPv4网络互联层也有把原始数据报自动分段成用于传输的更小单元。在这种情况下，IP协议会对无序交付的分段进行重新排序。
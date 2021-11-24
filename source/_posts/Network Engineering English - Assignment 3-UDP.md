---
title: Network Engineering English - Assignment 3-UDP
date: 2021-10-22 10:28:31
updated:
tags: TCP/IP
categories: 文章翻译
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

# Network Engineering English - Assignment 3-UDP

User Datagram Protocol（用户数据报协议）

The User Datagram Protocol (UDP) is one of the core members of the Internet Protocol Suite, the set of network protocols used for the Internet.

> 用户数据报协议（UDP）是互联网协议集中的重要成员之一，也是用于互联网的网络协议集合之一。

With UDP, computer applications can send messages, in this case referred to as datagrams, to other hosts on an Internet Protocol (IP) network without requiring prior communications to set up special transmission channels or data paths.

> 通过应用UDP协议，计算机应用程序可以通过建立特殊的传输频道或数据路径发送也被称为数据报的信息给其它处于IP网络中的不要求优先通信的主机。

The protocol was designed by David P. Reed in 1980 and formally defined in RFC 768. 

> David P. Reed在1980年设计了这个协议并正式定义为RFC 768.

UDP uses a simple transmission model without implicit hand-shaking dialogues for guaranteeing reliability, ordering, or data integrity.

> UDP协议使用了没有包括为了保证可靠性、有序性和数据完整性的隐式握手对话的简单传输模型。

Thus, UDP provides an unreliable service and datagrams may arrive out of order, appear duplicated, or go missing without notice.

> 因此，UDP协议提供的是不可靠的服务，并且数据报可能会无序到达，出现重复，或是没有另行通知的数据丢失。

UDP assumes that error checking and correction is either not necessary or performed in the application, avoiding the overhead of such processing at the network interface level.

> UDP协议假定了错误检查和纠正不是必要的或是错误表现在应用程序中，避免了这种在网络接口层面的进程的系统开销。

Time-sensitive applications often use UDP because dropping packets is preferable to waiting for delayed packets, which may not be an option in a real-time system.

> 对于时间较敏感的应用程序常常使用UDP协议，因为在一个实时的系统这可能不是一个选项而对于等待延迟数据包来说丢弃数据包是更可取的。

If error correction facilities are needed at the network interface level, an application may use the Transmission Control Protocol (TCP) or Stream Control Transmission Protocol (SCTP) which are designed for this purpose.

> 如果网络接口层面需要错误纠正设备，应用程序可能会使用为这种目的设计的传输控制协议协议（TCP）或是流控制传输协议（SCTP）。

UDP's stateless nature is also useful for servers answering small queries from huge numbers of clients.

> UDP协议的无状态的特性也可以用于服务器回复从大量客户端发来的质疑。

Unlike TCP, UDP is compatible with packet broadcast (sending to all on local network) and multicasting (send to all subscribers).

> 不像TCP协议，UDP协议与数据包广播（发送到所有在本地网络上的主机）和多点广播（发送到所有订阅者）一致。

Common network applications that use UDP include: the Domain Name System (DNS), streaming media applications such as IPTV, Voice over IP (VoIP) and many online games.

> 使用UDP协议的常见的网络应用程序有：域名解析系统（DNS），流媒体应用程序例如IP电视，网络语音电话业务（VolP）和许多在线游戏。
---
title: <sgg视频学习>Netty——1. BIO
date: 2021-11-09 16:38:33
updated:
tags: BIO
categories: Netty
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

#### Netty的介绍

1. Netty是由JBOSS提供的一个Java开源框架，现为Github上的独立项目
2. Netty是一个异步的、基于事件驱动的网络应用框架，用以快速开发高性能、高可靠性的网络IO程序
3. Netty主要针对在TCP/IP协议下，面向Clients端的高并发应用，或者Peer-to-Peer场景下的大量数据持续传输应用
4. Netty本质是一个NIO框架，适用于服务器通讯相关的多种应用场景

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111091722733.png)

#### IO模型

1. I/O模型的简单理解：就是用什么样的通道进行数据的发送和接收，很大程度上决定了程序通信的性能
2. Java共支持3种网络编程模型I/O模式：BIO、NIO、AIO
3. Java BIO：同步并阻塞（**传统阻塞型**），服务器实现模式为一个连接一个线程，如果这个连接不做任何事情就会造成不必要的线程开销
4. Java NIO：**同步非阻塞**，服务器实现模式为一个线程处理多个连接，即客户端发送的连接请求都会注册到多路复用服务器上，多路复用服务器轮询到连接有I/O请求就进行处理
5. Java AIO（NIO.2）：**异步非阻塞**，AIO引入异步通道的概念，采用Proactor模式，简化了程序编写，有效请求才启动线程，它的特点是先由操作系统完成后才通知服务端程序启动线程去处理，一般适用于连接数较多且连接时间较长的应用

#### IO模型应用

+ BIO：连接项目较小且固定
+ NIO：连接数目多且连接较短的架构
+ AIO：连接数目多且连接较长的架构

 #### BIO

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111091727785.png)

`java.io` 阻塞同步 可以通过线程池改善（实现多个客户连接服务器）

简单流程

+ 服务端启动`ServerSocket`
+ 客户端启动`Socket`对服务器进行通信
+ 如果服务器端有线程，等待/拒绝
+ 如果有响应，客户端线程会等待请求结束后再继续执行

```java
package fujang;

import java.io.IOException;
import java.io.InputStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.concurrent.Executor;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class BIOServer {
    public static void main(String[] args) throws IOException {

        ExecutorService executorService = Executors.newCachedThreadPool();

        ServerSocket serverSocket = new ServerSocket(6666);

        System.out.println("Server has exced");

        while (true) {
            final Socket socket = serverSocket.accept(); //阻塞

            System.out.println("one client has connected...");

            executorService.execute(new Runnable() {
                public void run() {
                    handler(socket);
                }
            });
        }
    }

    public static void handler(Socket socket) {
        try {
            System.out.println(Thread.currentThread().getId() + "/" + Thread.currentThread().getName());

            byte[] bytes = new byte[1024];

            InputStream inputStream = socket.getInputStream();

            while (true) {
                int read = inputStream.read(bytes); //阻塞
                if (read != -1) {
                    System.out.println(new String(bytes, 0, read));
                } else {
                    break;
                }
            }

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            System.out.println("connection has been closed...");
            try {
                socket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

`telnet1`

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111091613695.png)

`telnet2`

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111091615029.png)

`konsole`

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111091617391.png)
































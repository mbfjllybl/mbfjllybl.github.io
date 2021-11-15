---
title: <sgg视频学习>Netty——3. NIO
date: 2021-11-11 18:38:33
updated:
tags: NIO
categories:
- [网络编程]
- [Netty]
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

### Selector

#### 基本介绍

+ Java的NIO，用非阻塞的IO方式，可以用一个线程，处理多个客户端连接，就会用到Selector选择器。
+ Selector能够检测多个注册的通道上是否有事件发生（多个channel以事件的方式可以注册到同一个Selector），如果有事件发生，便获取事件然后针对每个事件进行相应的处理，这样就可以只用一个单线程去管理多个通道，也就是管理多个连接和请求。
+ 只有在连接真正有读写事件发生时，才会进行读写，就大大地减少了系统开销，并且不必为每个连接都创建一个线程，不用去维护多个线程
+ 避免了多线程之间上下文切换导致的开销

#### 特点再说明

+ Netty的IO线程NioEventLoop聚合了Selector（选择器，也叫多路复用器）可以同时并发处理成百上千个客户端连接
+ 当线程从某客户端Socket通道进行读写数据时，若没有数据可用时，该线程可以进行其他业务
+ 线程通常将非阻塞IO的空闲时间用于在其他管道上执行IO操作，所以单独的线程可以管理多个输入和输出管道
+ 由于读写操作都是非阻塞的，这就可以充分的提升IO线程的运行效率，避免由于频繁IO阻塞导致的线程挂起
+ 一个IO线程可以并发处理N个客户端连接和读写操作，这从根本上解决了传统同步阻塞IO一连接一线程模型，性能的架构、弹性伸缩能力和可靠性都得到了极大的提升

#### 方法

Selecter类是一个抽象类

`public abstract class Selector implements Closeable`

`public static Selector open` 得到一个选择器对象

`public int select(long timeout)` 监控所有注册的通道，当其中有IO操作进行时，将对应的SelectionKey加入到集合中并返回，参数用来设置超时时间

`public Set<SelectionKey> selectedKeys()` 从内部集合中得到所有的SelectionKey

#### 注意事项

NIO的ServerSocketChannel功能类似于ServerSocket，SocketChannel功能类似Socket

Selector相关方法说明

+ selector.select() 阻塞
+ selector.select(1000) 阻塞1000ms后返回
+ selector.wakeup() 唤醒selector
+ selector.selectNow() 不阻塞立马返回

#### 理解

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111112157305.png)

1. 当客户端连接时，会通过ServerSocketChannel得到SocketChannel
2. selector开始监听，Selector进行监听selector方法，返回有事件发生的通道个数
3. 将SocketChannel注册到Selector上，`register(Selector sel, int ops)`，一个selector上可以注册多个SocketChannel
4. 注册后返回一个SelectionKey，会和该Selector关联（集合）
5. 进一步得到各个SelectionKey（有事件发生）
6. 通过SelectionKey反向获取SocketChannel，方法channel()
7. 可以通过得到的channel,完成业务处理

#### NIOServer

```java
package selector;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.*;
import java.util.Iterator;
import java.util.Set;

public class NIOServer {
    public static void main(String[] args) throws IOException {
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        Selector selector = Selector.open();
        serverSocketChannel.socket().bind(new InetSocketAddress(6666));
        //设置为非阻塞
        serverSocketChannel.configureBlocking(false);
        //serverSocketChannel注册到channel 关心事件为藕OP_ACCEPT
        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
        //循环等待连接
        while (true) {
            if (selector.select(1000) == 0) {
                System.out.println("nothing happened, no connection, wait 1s...");
                continue;
            }
            //有事件发生的selectionKeys
            Set<SelectionKey> selectionKeys = selector.selectedKeys();

            Iterator<SelectionKey> keyIterator = selectionKeys.iterator();
            while (keyIterator.hasNext()) {
                SelectionKey key = keyIterator.next();
                if (key.isAcceptable()) {
                    SocketChannel socketChannel = serverSocketChannel.accept();
                    System.out.println("Client successfully connected " + socketChannel.hashCode());
                    //关注事件为读，同时关联一个buffer
                    socketChannel.configureBlocking(false);
                    socketChannel.register(selector, SelectionKey.OP_READ, ByteBuffer.allocate(1024));

                }
                if (key.isReadable()) {
                    SocketChannel channel = (SocketChannel)key.channel();
                    ByteBuffer buffer = (ByteBuffer) key.attachment();
                    channel.read(buffer);
                    System.out.println("from client" + new String(buffer.array()));
                }
                //移除当前的selectionKey
                keyIterator.remove();
            }
        }
    }
}
```

#### NIOClient

```
package selector;

import javax.naming.ldap.SortKey;
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SocketChannel;

public class NIOClient {
    public static void main(String[] args) throws IOException {
        SocketChannel socketChannel = SocketChannel.open();
        socketChannel.configureBlocking(false);
        //提供服务器端的ip和端口
        InetSocketAddress inetSocketAddress = new InetSocketAddress("127.0.0.1", 6666);
        if (!socketChannel.connect(inetSocketAddress)) {
            while (!socketChannel.finishConnect()) {
                System.out.println("because connection need time, and client will not blocked, we can do smthing else");
            }
        }
        String str = "hello fujang!";
        //包裹数组到指定数组大小的buffer中去
        ByteBuffer buffer = ByteBuffer.wrap(str.getBytes());
        socketChannel.write(buffer);
        System.in.read();
    }
}
```

 #### SelectionKey

表示Selector和网络通道的注册关系，共4种

`public static final int OP_READ = 1 << 0;` 读操作

`public static final int OP_WRITE = 1 << 2;` 写操作

`public static final int OP_CONNECT = 1 << 3;` 连接已建立

`public static final int OP_ACCEPT = 1 << 4;` 有新的网络可以acept

`Selector()` 得到与之关联的Selector

`channel() `得到与之关联的channel

`attachment()` 得到与之关联的共享数据

`interestOps(int ops)` 设置或改变监听事件

`isAcceptable()` 是否可以accept

`isReadable()` 是否可以读

`isWritable()` 是否可以写

#### ServerSocketChannel

在服务器监听新的客户端Socket连接

`static open()` 得到一个ServerSocketChannel

`build(SocketAddress local)` 设置服务器端口号

`configureBlocking(boolean block)` 设置阻塞和非阻塞

`accept()` 接受一个连接，返回连接对应的SocketChannel

`register(Selector sle, int ops)`注册一个选择器并设置监听事件

#### SocketChannel

`static open()` 得到一个SocketChannel通道

`configureBlocking(boolean block)` 设置阻塞和非阻塞

`connect(SocketAddress remote)` 连接服务器

`finishConnect()`如果上面的方法连接失败，那么就用该方法完成连接操作

`write(ByteBuffer src)` 往通道写数据

`read(ByteBuffer dst)`从通道读数据

`register(Selector sel, int ops, Object att)` 注册一个选择器，并设置监听事件，最后一个参数可以设置共享数据

`close()`关闭通道
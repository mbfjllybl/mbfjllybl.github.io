---
title: <sgg视频学习>Netty——2. NIO
date: 2021-11-11 16:38:33
updated:
tags: NIO
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

#### NIO基本介绍

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111091827844.png)

1. 该图反映了3个Channel注册到Selector上
2. 程序切换到哪个Channel由事件决定，Event是一个重要概念
3. Selector根据不同的事件，在各个通道上切换
4. Buffer内存块底层是一个数组，既可以读又可以写，需要`flip`方法切换
5. Channel为双向的，可以返回底层操作系统的情况
6. **Thread和Selector之间也有buffer**

---

`java.nio` `Java non-blocking IO` 同步非阻塞

+ NIO三大核心部分：`Channel`（通道）、`Buffer`（缓冲区）、`Selector`（选择器）
+ NIO是面向缓冲区，或者面向块编程的，数据读取到一个它稍后处理的缓冲区，需要时可在缓冲区中前后移动，这就增加了处理过程中的灵活性，使用它可以提供非阻塞式伸缩性网络。
+ 一个线程从某通道发送请求或读取请求数据，但是它仅能得到目前可用的数据，如果目前没有数据可用，就什么都不会获取，而不是保持线程阻塞，所以直到数据变得可以读取之前，该线程还可以继续做其他的事情，非阻塞写也是如此，一个线程请求写入一些数据到某通道，但不需要等待它完全写入，这个线程同时可以去做别的事情
+ NIO可以做到用一个线程来处理多个操作，假设有10000个请求过来，根据实际情况，可能分配50个或100个线程来处理，而不像之前阻塞IO那样，得分配10000个
+ HTTP2.0使用了多路复用技术，做到一个连接并发处理多个请求，而且并发请求的数量比HTTP1.1大了好几个数量级

#### BIO和NIO的比较

+ BIO以流的方式处理数据，NIO以块的方式处理数据，块NIO的效率高
+ BIO是阻塞的，NIO是非阻塞的
+ BIO是基于字节流和字节流，NIO是基于Channel和Buffer，数据总是从通道到缓冲区，或者从缓冲区到通道，Selector用于监听多个通道的事件，因此使用单个线程就可以监听多个客户端通道

#### Buffer

```java
package fujang;

import java.nio.DoubleBuffer;
import java.nio.IntBuffer;

public class BasicBuffer {
    public static void main(String[] args) {
        // 创建一个buffer,大小为5个int
        IntBuffer intBuffer = IntBuffer.allocate(5);
        // 在buffer中放入数据
        for (int i = 0; i < intBuffer.capacity(); i++) {
            intBuffer.put(i);
        }

        // 将buffer读写切换
        intBuffer.flip();

        while (intBuffer.hasRemaining()) {
            // get取出
            System.out.println(intBuffer.get());
        }

    }
}
```

Buffer为一个顶层父类，且为抽象类

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111091834165.png)

Buffer中的属性

```java
private int mark = -1; //标记
private int position = 0; //下一个要操作的位置
private int limit; //数组终点
private int capacity; //最大容量初始值
```

Buffer中的方法

```java
clear 数据并没有真正删除，只是各个标记恢复到初始状态
flip
limit(int newLimit)
limit
position(int newPosition)
position
capacity 
hasRemaining
isReadOnly
hasArray
Array
```

最常用的ByteBuffer中的方法

```java
allocateDirect 创建直接缓冲区
allocate(int capacity) 设置缓冲区的初始容量
get position自动+1
get(int index) position不变
put position自动+1
put(int index) position不变
```

+ ByteBuffer支持类型化的put和get，put什么类型的数据，get就应该使用相应的数据类型取出来，否则会报异常
+ 可以将一个普通buffer转为只读buffer

+ MappedByteBuffer，可以让文件直接在内存（堆外的内存）中进行修改，而如何同步到文件由NIO来完成

```java
package fujang;

import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.RandomAccessFile;
import java.nio.MappedByteBuffer;
import java.nio.channels.FileChannel;

//文件直接在内存（堆外内存）修改，操作系统不需要拷贝一次
public class MappedByteBufferTest {
    public static void main(String[] args) throws IOException {
        RandomAccessFile randomAccessFile = new RandomAccessFile("/home/mbfjllybl/IdeaProjects/netty-test/NIO/src/main/java/fujang/1.txt", "rw");
        FileChannel channel = randomAccessFile.getChannel();
        // 读写模式 起始位置0,范围0-5,不包括5
        MappedByteBuffer mappedByteBuffer = channel.map(FileChannel.MapMode.READ_WRITE, 0, 5);
        mappedByteBuffer.put(0, (byte)'Q');
        randomAccessFile.close();
    }
}
```

+ NIO还支持通过多个Buffer（即Buffer数组）完成读写操作，即Scattering和Gathering

```
package fujang;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.Buffer;
import java.nio.ByteBuffer;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;
import java.util.Arrays;

/**
 * Scattering：将数据写入buffer时，可以采用buffer数组，依次写入（分散）
 * Gathering：从buffer读取数据时，可以采用buffer数组，依次读
 */
public class ScatteringAndGatheringTest {
    public static void main(String[] args) throws IOException {
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        InetSocketAddress inetSocketAddress = new InetSocketAddress(7000);

        serverSocketChannel.socket().bind(inetSocketAddress);

        ByteBuffer[] byteBuffers = new ByteBuffer[2];
        byteBuffers[0] = ByteBuffer.allocate(5);
        byteBuffers[1] = ByteBuffer.allocate(3);

        SocketChannel socketChannel = serverSocketChannel.accept();

        int messageLength = 8;

        while (true) {
            int byteRead = 0;

            while (byteRead < messageLength) {
                long l = socketChannel.read(byteBuffers);
                byteRead += l;
                System.out.println(new String(byteBuffers[0].array()));
                System.out.println("now has read " + byteRead + " bytes");
                Arrays.asList(byteBuffers).stream().map(buffer -> "position = " + buffer.position() + ", limit = " + buffer.limit()).forEach(System.out::println);
            }
            Arrays.asList(byteBuffers).stream().forEach(buffer -> buffer.flip());
            long byteWrite = 0;
            while (byteWrite < messageLength) {
                long l = socketChannel.write(byteBuffers);
                byteWrite += l;
            }
            Arrays.asList(byteBuffers).stream().forEach(buffer -> buffer.clear());

            System.out.println("byteRead = " + byteRead + " byteWrite = " + byteWrite + " messageLength = " + messageLength);
        }
    }
}
```

#### Channel

+ 可以同时读写
+ 可以异步读写
+ 可以写数据到缓冲，也可以从缓冲读数据

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111091907165.png)

+ `SocketChannel`、`ServerSocketChannel`：基于TCP
  + `SocketChannel`：相当于图中`Channel`部分
  + `ServerSocketChannel`：相当于`Service`部分
+ `DatagramChannel`：基于UDP
+ `FileChannel`：基于文件

FileChannel中常用的方法

```java
read(ByteBuffer dst)
write(ByteBuffer src)
transferFrom 从目标通道中复制数据到当前通道
transferTo 从当前通道中复制数据到目标通道
```

本地文件写

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111091934115.png)

```java
package fujang;

import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;

public class NIOFileChannel01 {
    public static void main(String[] args) throws IOException {
        String str = "Hello fujang";
        FileOutputStream fileOutputStream = new FileOutputStream("/home/mbfjllybl/IdeaProjects/netty-test/NIO/src/main/java/fujang/1.txt");

        //实际类型 FileChannelImpl
        FileChannel fileChannel = fileOutputStream.getChannel();

        ByteBuffer byteBuffer = ByteBuffer.allocate(1024);

        byteBuffer.put(str.getBytes());

        byteBuffer.flip();

        fileChannel.write(byteBuffer);

        fileOutputStream.close();

    }
}
```

本地文件读

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111091950793.png)

```java
package fujang;

import java.io.*;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;

public class NOIFileChannel02 {
    public static void main(String[] args) throws IOException {
        
        File file = new File("/home/mbfjllybl/IdeaProjects/netty-test/NIO/src/main/java/fujang/1.txt");
        
        FileInputStream fileInputStream = new FileInputStream(file);
        
        FileChannel channel = fileInputStream.getChannel();
        
        ByteBuffer byteBuffer = ByteBuffer.allocate((int)file.length());
        
        channel.read(byteBuffer);
        
        System.out.println(new String(byteBuffer.array()));
        
        fileInputStream.close();
        
    }
}
```

本地文件复制

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111091950955.png)

```java
package fujang;

import java.io.*;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;

public class NOIFileChannel03 {
    public static void main(String[] args) throws IOException {

        File file1 = new File("/home/mbfjllybl/IdeaProjects/netty-test/NIO/src/main/java/fujang/1.txt");
        FileInputStream fileInputStream1 = new FileInputStream(file1);
        FileChannel channel1 = fileInputStream1.getChannel();

        File file2 = new File("/home/mbfjllybl/IdeaProjects/netty-test/NIO/src/main/java/fujang/2.txt");
        FileOutputStream fileOutputStream2 = new FileOutputStream(file2);
        FileChannel channel2 = fileOutputStream2.getChannel();

        ByteBuffer byteBuffer = ByteBuffer.allocate(512);

        while (true) {

            byteBuffer.clear();

            int read = channel1.read(byteBuffer);

            if (read == -1) {
                break;
            }

            byteBuffer.flip();
            channel2.write(byteBuffer);
        }

        fileInputStream1.close();
        fileOutputStream2.close();

    }
}
```

transferFrom

```java
package fujang;

import java.io.*;
import java.nio.channels.FileChannel;

public class NIOFileChannel04 {
    public static void main(String[] args) throws IOException {
        File file1 = new File("/home/mbfjllybl/IdeaProjects/netty-test/NIO/src/main/java/fujang/1.txt");
        FileInputStream fileInputStream1 = new FileInputStream(file1);
        FileChannel channel1 = fileInputStream1.getChannel();

        File file2 = new File("/home/mbfjllybl/IdeaProjects/netty-test/NIO/src/main/java/fujang/cp.txt");
        FileOutputStream fileOutputStream2 = new FileOutputStream(file2);
        FileChannel channel2 = fileOutputStream2.getChannel();

        channel2.transferFrom(channel1, 0, channel1.size());

        fileInputStream1.close();
        fileOutputStream2.close();

    }
}
```


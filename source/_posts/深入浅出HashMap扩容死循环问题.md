---
title: 深入浅出HashMap扩容死循环问题
date: 2021-11-03 11:38:33
updated:
tags: HashMap
categories:
- [Java]
- [技术转载学习]
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

# 深入浅出HashMap扩容死循环问题

## 一.问题

众所周知，HashMap是线程不安全的，在并发使用HashMap时很容易出现一些问题，其中最典型的就是并发情况下扩容之后会发生死循环，导致CPU占用100%。同时，这也是一个高频面试题。因此，本文通过解读HashMap源码并结合实例，来具体分析HashMap扩容发生的死循环问题。

## 二.源码解读

下面这段代码是JDK 1.7中HashMap的resize方法，即扩容时调用的代码，作用是创建新的Entry数组newTable，然后调用transfer方法将原来的Entry数组中的节点都转移到newTable中，最后将HashMap的成员变量table指向newTable，所以扩容机制的核心代码在transfer方法中。

```java
void resize(int newCapacity) {
    Entry[] oldTable = table;
    int oldCapacity = oldTable.length;
    if (oldCapacity == MAXIMUM_CAPACITY) {
        threshold = Integer.MAX_VALUE;
        return;
    }

    Entry[] newTable = new Entry[newCapacity];
    transfer(newTable, initHashSeedAsNeeded(newCapacity));
    table = newTable;
    threshold = (int)Math.min(newCapacity * loadFactor, MAXIMUM_CAPACITY + 1);
}
```

下面这段代码是JDK1.7中HashMap的transfer方法，作用是遍历原来table中每个位置的链表，并对链表中的每个节点重新进行hash，在新的Entry数组newTable中找到归宿，并插入。

```java
void transfer(Entry[] newTable, boolean rehash) {
    int newCapacity = newTable.length;
    for (Entry<K,V> e : table) {
        //获取链表的头节点e
        while(null != e) {
            //获取要转移的下一个节点next
            Entry<K,V> next = e.next;
            if (rehash) {
                e.hash = null == e.key ? 0 : hash(e.key);
            }
            //计算要转移的节点在新的Entry数组newTable中的位置
            int i = indexFor(e.hash, newCapacity);
            //使用头插法将要转移的节点插入到newTable原有的单链表中
            e.next = newTable[i];
            //将newTable的hash桶的指针指向要转移的节点
            newTable[i] = e;
            //转移下一个需要转移的节点e
            e = next;
        }
    }
}
```

其中，transfer方法的核心代码分为以下四个步骤：

1. $Entry<K,V> next = e.next;$获取要转移的下一个节点next

2. $e.next = newTable[i];$使用头插法将要转移的节点插入到newTable原有的单链表中
3. $newTable[i] = e;$将newTable的hash桶的指针指向要转移的节点
4.  $e = next;$转移下一个需要转移的节点e

## 三.实例分析

### 1.单线程扩容

假设单线程场景下要对下图中的table进行扩容：

<img src="https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111031020496.webp" style="zoom:50%;" />

扩容初始情况如下：

<img src="https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111031021222.webp" style="zoom:50%;" />

转移A节点之后的情况如下：

<img src="https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111031021746.webp" style="zoom:50%;" />

为了便于理解，简化上图如下：

<img src="https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111031022474.webp" style="zoom:50%;" />

转移B节点之后的情况如下：

<img src="https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111031022709.webp" style="zoom:50%;" />

转移C节点之后，oldTable中的节点就都转移到newTable中了，HashMap成功完成了扩容的过程：

<img src="https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111031022063.webp" style="zoom:50%;" />

观察扩容之前的oldTable中的单链表和扩容之后的newTable中的单链表，不难发现，oldTable中的单链表和newTable中的单链表的节点的顺序是相反的。由于扩容过程中使用头插法将oldTable中的单链表中的节点插入到newTable的单链表中，所以newTable中的单链表会倒置oldTable中的单链表。

### 2.多线程扩容

假设有两个线程同时对HashMap进行扩容，在某一时刻这两个线程都进行到如下状态：

<img src="https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111031023378.webp" style="zoom:50%;" />

之后线程1执行了transfer方法的核心代码中的第1步和第2步，时间片就用完了，执行后的情况如下：

<img src="https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111031024439.webp" style="zoom:50%;" />

这时轮到线程2开始执行，执行transfer方法的核心代码中的第1步之后，情况如下：

<img src="https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111031024391.webp" style="zoom:50%;" />

线程2执行第2，3，4步之后，情况如下：

<img src="https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111031024250.webp" style="zoom:50%;" />

线程2执行下一轮循环的第1，2，3，4步之后，线程2的扩容过程成功结束：

<img src="https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111031025063.webp" style="zoom:50%;" />

可以看到，线程2扩容之后的newTable中的单链表形成了一个环，后续执行get操作的时候，会触发死循环，引起CPU的100%问题。

### 四.总结

通过解读HashMap源码并结合实例可以发现，HashMap扩容导致死循环的主要原因在于**扩容过程中使用头插法将oldTable中的单链表中的节点插入到newTable的单链表中**，所以newTable中的单链表会倒置oldTable中的单链表。那么在多个线程同时扩容的情况下就可能导致**扩容后的HashMap中存在一个有环的单链表**，从而导致后续执行get操作的时候，会触发死循环，引起CPU的100%问题。所以一定要避免在并发环境下使用HashMap。

## 五.转载

> [link](https://www.jianshu.com/p/4d1cad21853b?utm_campaign=hugo)


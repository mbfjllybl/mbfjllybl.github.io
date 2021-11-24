---
title: <Redis设计与实现>第一部分 数据结构与对象
date: 2021-11-05 01:38:33
updated:
tags: Redis
categories: Redis
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

# <Redis设计与实现>第一部分 数据结构与对象

## 1. 简单动态字符串SDS

+ 应用：

  除了用来保存数据库中的字符串值之外，SDS还被用作缓冲区（buffer）；AOF模块中的AOF缓冲区，以及客户端状态中的输入缓冲区，都是由SDS实现的。

+ SDS与C字符串的区别

  + 常数复杂度获取字符串长度
  + 杜绝缓冲区溢出：当SDS API要对SDS进行修改时，API会先检查SDS空间是否满足修改需求，如果不满足的话，SDS会自动扩展空间至所需的大小
  + 减少修改字符串所带来的内存重新分配次数：1.**空间预分配**（当对SDS空间修改时，也就是内存重新分配时，如果长度小于1MB，则FREE和LEN值相同，BUF数组上都为FREE+LEN+1；如果大于1MB，那么程序会分配1MB未使用空间） 2.**惰性空间释放**（LEN减少时，用FREE记录空出的空间以便将来使用）
  + 二进制安全：BUF数组用二进制存储数据，所有API都以处理二进制的方式处理数据，BUF被称为字节数组
  + 兼容部分C字符串函数

## 2. 链表

+ 应用：
  + 列表键：当列表元素较多时，或者元素都是比较长的字符串时，会使用链表
  + 发布与订阅、慢查询、监听器等功能，Redis服务器本身还使用链表来保存多个客户端的状态信息，以及使用链表来构建客户端缓冲区
+ 特点：
  + 双端
  + 无环
  + 带头指针和尾指针
  + 带链表长度的计数器
  + 多态：链表内部函数：dup、free、match

## 3. 字典

+ 应用：
  + Redis数据库底层、哈希键的底层实现之一（当键值对比较多，又或者键值对中的元素都是比较长的字符串时）
+ 字典底层实现为哈希表，用链表解决哈希冲突的问题，头插。底层有两个哈希表，一个存储，一个rehash时使用，rehashidx记录了rehash进度，如果未进行REHASH，值为-1
+ 根据键计算哈希值（Murmurhash2），根据 sizemask（size-1）和哈希值计算索引值
+ 扩展：服务器没有执行BGSAVE或BGREWRITEAOF命令，负载因子大于等于1；执行命令，复杂因子大于等于5，这是因为执行这些命令时会用COW产生子进程，避免更改父进程来产生不必要的写操作
+ 收缩：负载因子小于等于0.1
+ ht[1]大小
  + 扩展：第一个大于等于`ht[0].used*2`的$2^n$
  + 收缩：第一个大于等于`ht[0].used`的$2^n$
+ 将ht[0]上的值**渐进式**rehash到ht[1]，再把ht[0]释放，再把ht[1]变为ht[0]，ht[1]为空白。rehashidx记录索引，每次对字典操作时，都对rehashidx上的索引rehash。把计算工作均摊到每次操作上。在此期间操作先查询ht[0]，再查询ht[1]；如果是增加，则加到ht[1]中

## 4. 跳跃表

+ 应用：

  + Redis使用跳跃表作为有序集合键的底层实现之一，如果一个有序集合包含的元素数量比较多，又或者有序集合中元素的成员是比较长的字符串时，Redis就会使用跳跃表作为有序集合键的实现。另一个实现是在集群节点中作内部数据结构。

  ![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111050123304.png)

跳跃表节点的实现如上，每生成一个新节点时，都会根据幂次定律随机生成一个1到32的数作为level的大小。

```c
typedef struct zskiplistNode {
    struct zskiplistList {
        struct zskiplistList *forward;
        unsigned int span;
    } level[];
    struct zskiplistNode *backward;
    double score;
    robj *obj;
} zskiplistNode;
```

+ 分值相同的点按对象字典序的大小排序

## 5. 整数集合

+ 应用：
  + 整数集合是集合键的底层实现之一，当一个集合只包含整数值元素，并且这个集合的元素数量不多时，Redis就会使用整数集合作为集合键的实现。

+ `intset`实现中底层数组的真正类型取决于内部`encoding`的属性，为`int16_t`、`int32_t`、`int64_t`。
+ 当增加的数据类型长度大于内部数组的类型，就会进行升级，所以添加元素的时间复杂度为$O(n)$，此时造成升级的数据要么在数组的最开头要么在最后；不支持降级操作。提升操作灵活性、节约内存。

## 6. 压缩列表

+ 为了节约内存而开发的数据结构。

+ 应用：

  + 压缩列表是列表键和哈希键的底层实现之一。当一个列表只包含少量列表项，并且每个列表项要么是较小的数值，要么是长度小的字符串，底层实现就会为压缩列表。同理，当哈希键只有少量的键值对且长度较短的字符串或较小的数值，底层实现就为压缩列表。结构如下

  ![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111050124346.png)

+ 压缩列表的节点

  ![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111050125322.png)

  + `previous_entry_length`：前一个节点长度
  + `encoding`：当前节点类型
  + `content`：字节数组或者整数

+ 连锁更新：特殊情况下添加或删除节点造成连续多次空间扩展，虽然复杂度为$O(n)$，但是影响不大。

  + 并不多见
  + 更新的节点数量不多

## 7. 对象

+ `type`命令输出类型，`object encoding`命令输出编码。

  ![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111050125038.png)

+ Redis根据不同的使用常见来为一个对象设置不同的编码，从而优化对象在某一场景下的效率。

  ![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111050132162.png)

### 7.1 string

+ `raw`会调用两次内存分配函数，一次为`redisObject`一次为`sdshdr`；而embstr编码通过一次内存分配函数来分配一块连续的内存，空间中依次包含两个结构。

  + raw内存分配两次，embstr一次
  + 释放raw编码的对象需要两次释放函数，embstr一次
  + embstr是连续的内存，更好利用缓存带来的优势
  + embstr为只读，因为没有编写相关的修改程序，因此修改前先变成raw

+ 浮点数也为字符串，运算时重新转换为字符串。

  ![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111050129240.png)

### 7.2 list

+ 双端链表的节点可能包含多个字符串对象。

  ![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111050130608.png)

### 7.3 hash

+ 用压缩链表保存的哈希对象的键值对相邻，键在前值在后。

+ 如果用字典存储，那么键和值都是字符串对象。

  ![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111050130540.png)

### 7.4 set

+ 如果用字典存储，那么键为字符串对象，值为NULL

  ![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111050131317.png)

### 7.5 zset

+ 压缩链表保存的对象和`score`相邻，对象在前，`score`在后。

+ ```c
  typedef struct zset {
      zskiplist *zsl;
      dict *dict;
  } zset;
  ```

  ![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111050131929.png)

  字典和跳跃表通过指针共享对象，不会造成内存浪费。

  ![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111050131541.png)

### 7.6 命令检查和命令多态

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111050132853.png)

+ DEL和EXPIRE是类型的多态，LLEN是编码的多态。

### 7.7 内存回收

+ 新对象的引用计数`refcount`为1
+ 被新程序使用`+1`
+ 不再被程序使用`-1`
+ 为`0`时所占内存被释放

### 7.8 内存共享

+ 被共享的对象`refcount++`
+ Redis只对包含整数值的字符串对象进行共享
+ Redis在初始化服务器时，创建1w个字符串对象，分别为0到9999，进行共享。

### 7.9 对象的空转时长

+ 对象的`lru`属性：最后一次访问时间
+ `object idletime`：空转时长
+ 服务器打开了maxmemory选项，回收算法为`volatile-lru`或`allkeys-lru`，时长大的对象优先被服务器释放，回收内存


---
title: Java 中 HashMap 初始化时赋值
date: 2021-11-03 23:38:33
updated:
tags: Java
categories: Java
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

# Java 中 HashMap 初始化时赋值

1、HashMap 初始化的文艺写法

HashMap 是一种常用的数据结构，一般用来做数据字典或者 Hash 查找的容器。普通青年一般会这么初始化：

```java
HashMap<String, String> map = new HashMap<String, String>();
map.put("name", "test");  
map.put("age", "20");
```

看完这段代码，很多人都会觉得这么写太啰嗦了，文艺青年一般这么来了：

```java
HashMap<String, String> map = new HashMap<String, String>() {
    {
        put("name", "test");  
        put("age", "20"); 
    }
};
```

看起来优雅了不少，一步到位，一气呵成的赶脚。然后问题来了，这里的双括号`”{{}}”`到底什么意思，什么用法呢？

双括号`”{{}}”`,用来初始化，使代码简洁易读。

第一层括弧实际是定义了一个匿名内部类 (Anonymous Inner Class)，第二层括弧实际上是一个实例初始化块 (instance initializer block)，这个块在内部匿名类构造时被执行。

2、推而广之，可初始化ArrayList、Set

```java
List<String> names = new ArrayList<String>() {
    {
        for (int i = 0; i < 10; i++) {
            add("A" + i);
        }
    }
};
System.out.println(names.toString());
```

3、Java 7：增加对collections的支持
在 Java 7 中你可以像 Ruby, Perl、Python 一样创建collections了， 但是这些集合是不可变的。

```java
List<String> list = ["item"];
String item = list[0];
  
Set<String> set = {"item"};
  
Map<String, Integer> map = {"key" : 1};
int value = map["key"];
```

4、文艺写法的潜在问题

文章开头提到的文艺写法的好处很明显就是一目了然。这里来罗列下此种方法的坏处，如果这个对象要串行化，可能会导致串行化失败。

1. 此种方式是匿名内部类的声明方式，所以引用中持有着外部类的引用。所以当时串行化这个集合时外部类也会被不知不觉的串行化，当外部类没有实现serialize接口时，就会报错。
2. 上例中，其实是声明了一个继承自HashMap的子类。然而有些串行化方法，例如要通过Gson串行化为json，或者要串行化为xml时，类库中提供的方式，是无法串行化Hashset或者HashMap的子类的，从而导致串行化失败。解决办法：重新初始化为一个HashMap对象：new HashMap(map);这样就可以正常初始化了。

5、执行效率问题

当一种新的工具或者写法出现时，猿们都会来一句：性能怎么样？（这和男生谈论妹纸第一句一般都是：“长得咋样？三围多少？”一个道理:)）关于这个两种写法我这边笔记本上测试文艺写法、普通写法分别创建 10,000,000 个 Map 的结果是 1217、1064，相差 13%。

```java
long startTime = System.currentTimeMillis();
Map<Integer, Integer> map = new HashMap<Integer, Integer>() {
    {
        for (int i = 0; i < 1000000; i++) {
            put(i, i);
        }
    }
};
log.info("cost time {} ms", System.currentTimeMillis() - startTime);

long startTime1 = System.currentTimeMillis();
Map<Integer, Integer> map2 = new HashMap<Integer, Integer>();
for (int i = 0; i < 100000; i++) {
    map2.put(i, i);
}

log.info("cost time {} ms", System.currentTimeMillis() - startTime1);
```

```sh
1. 0 2020-08-08 17:51:27 org.example.App INFO - cost time 136 ms
2. 8 2020-08-08 17:51:27 org.example.App INFO - cost time 8 ms
```


---
title: Java中遍历HashMap的5种方式
date: 2021-11-17 00:19:33
updated:
tags: Java
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

# Java中遍历HashMap的5种方式

本教程将为你展示Java中HashMap的几种典型遍历方式。

如果你使用Java8，由于该版本JDK支持lambda表达式，可以采用第5种方式来遍历。

如果你想使用泛型，可以参考方法3。如果你使用旧版JDK不支持泛型可以参考方法4。

### 1、通过ForEach循环进行遍历

```java
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
 
public class Test {
	public static void main(String[] args) throws IOException {
		Map<Integer, Integer> map = new HashMap<Integer, Integer>();
		map.put(1, 10);
		map.put(2, 20);
 
		// Iterating entries using a For Each loop
		for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
			System.out.println("Key = " + entry.getKey() + ", Value = " + entry.getValue());
		}
 
	}
}
```

### 2、ForEach迭代键值对方式

如果你只想使用键或者值，推荐使用如下方式

```java
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
 
public class Test {
	public static void main(String[] args) throws IOException {
		Map<Integer, Integer> map = new HashMap<Integer, Integer>();
		map.put(1, 10);
		map.put(2, 20);
 
		// 迭代键
		for (Integer key : map.keySet()) {
			System.out.println("Key = " + key);
		}
 
		// 迭代值
		for (Integer value : map.values()) {
			System.out.println("Value = " + value);
		}
	}
}
```

### 3、使用带泛型的迭代器进行遍历

```java
import java.io.IOException;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;
 
public class Test {
	public static void main(String[] args) throws IOException {
		Map<Integer, Integer> map = new HashMap<Integer, Integer>();
		map.put(1, 10);
		map.put(2, 20);
 
		Iterator<Map.Entry<Integer, Integer>> entries = map.entrySet().iterator();
		while (entries.hasNext()) {
			Map.Entry<Integer, Integer> entry = entries.next();
			System.out.println("Key = " + entry.getKey() + ", Value = " + entry.getValue());
		}
	}
}
```

### 4、使用不带泛型的迭代器进行遍历

```java
import java.io.IOException;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;
 
public class Test {
 
	public static void main(String[] args) throws IOException {
 
		Map map = new HashMap();
		map.put(1, 10);
		map.put(2, 20);
 
		Iterator<Map.Entry> entries = map.entrySet().iterator();
		while (entries.hasNext()) {
			Map.Entry entry = (Map.Entry) entries.next();
			Integer key = (Integer) entry.getKey();
			Integer value = (Integer) entry.getValue();
			System.out.println("Key = " + key + ", Value = " + value);
		}
	}
}
```

### 5、通过Java8 Lambda表达式遍历

```java
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
 
public class Test {
	public static void main(String[] args) throws IOException {
		Map<Integer, Integer> map = new HashMap<Integer, Integer>();
		map.put(1, 10);
		map.put(2, 20);
		map.forEach((k, v) -> System.out.println("key: " + k + " value:" + v));
	}
}
```

forEach在JDK 11中源码如下：

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111170037739.png)



> [link](https://blog.csdn.net/w605283073/article/details/80708943)


















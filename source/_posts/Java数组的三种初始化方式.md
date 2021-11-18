---
title: Java数组的三种初始化方式
date: 2021-11-08 01:38:33
updated:
tags: Java
categories:
- [Java]
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

# Java数组的三种初始化方式

Java语言中数组必须先初始化，然后才可以使用。所谓初始化就是为数组的数组元素分配内存空间，并为每个数组元素附初始值。

注意：数组完成初始化后，内存空间中针对该数组的各个元素就有个一个默认值：

基本数据类型的整数类型（byte、short、int、long）默认值是0；

基本数据类型的浮点类型（float、double）默认值是0.0；

基本数据类型的字符类型（char）默认值是`'\u0000'`；

基本数据类型的布尔类型（boolean）默认值是false；

类型的引用类型（类、数组、接口、String）默认值是null

 #### 初始化方式：

一、静态初始化：初始化时由程序员显式指定每个数组元素的初始值，由系统决定数组的长度；

`arrayName = new type[]{element1,element2,element3...}`

 示例：

```java
int[] intArr;
intArr = new int[]{1,2,3,4,5,9};
```

简化的静态初始化方式`type[] arrayName = {element1,element2,element3...};`

示例：

```java
String[] strArr = {"张三","李四","王二麻"};
```

二、动态初始化：初始化时由程序员指定数组的长度，由系统初始化每个数组元素的默认值。

` arrayName = new type[length];`

示例：

```java
int[] price = new int[4];
```

注意：不要同时使用静态初始化和动态初始化，也就是说，不要在进行数组初始化时，既指定数组的长度，也为每个数组元素分配初始值。

 一旦数组完成初始化，数组在内存中所占的空间将被固定下来，所以数组的长度将不可改变。
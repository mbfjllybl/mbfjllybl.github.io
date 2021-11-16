---
title: JAVA 中 CharSequence 类型
date: 2021-11-16 10:23:33
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

# JAVA 中 CharSequence 类型

charSequence是一个接口，表示char值的一个可读序列。此接口对许多不同种类的char序列提供统一的自读访问。此接口不修改该equals和hashCode方法的常规协定，因此，通常未定义比较实现 CharSequence 的两个对象的结果。他有几个实现类：CharBuffer、String、StringBuffer、StringBuilder。

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111161018339.png)

CharSequence与String都能用于定义字符串，但CharSequence的值是可读可写序列，而String的值是只读序列。

对于一个抽象类或者是接口类，不能使用new来进行赋值，但是可以通过以下的方式来进行实例的创建：`CharSequence cs="hello";`。对于抽象类或者接口来说不可以直接使用new的方式创建对象，但是可以直接给它赋值；`CharSequence b = "s"` 是一个类型强转操作，等于`CharSequence b = (CharSequence) new String("s")`。

实例：

```java
CharSequence cs;                             //声明一个CharSequence类型的变量cs
Resources res=context.getResources();        //定义一个Resources类型的变量res，并给它赋值
cs=res.getText(R.string.styled_text);        //获得R类中指定变量的值
tv=(TextView)findViewById(R.id.styled_text); //同上
tv.setText(cs);                              //设置值
```

> [link](https://blog.csdn.net/a78270528/article/details/46785949)
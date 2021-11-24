---
title: Java实例——队列Queue用法
date: 2021-10-31 11:38:33
updated:
tags: Queue
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

队列是一种特殊的线性表，它只允许在表的前端进行删除操作，而在表的后端进行插入操作。

``LinkedList``类实现了``Queue``接口，因此我们可以把``LinkedList``当成``Queue``来用。

以下实例演示了队列`Queue`的用法：

```java
import java.util.LinkedList;
import java.util.Queue;
 
public class Main {
    public static void main(String[] args) {
        //add()和remove()方法在失败的时候会抛出异常(不推荐)
        Queue<String> queue = new LinkedList<String>();
        
        //添加元素
        queue.offer("a");
        queue.offer("b");
        queue.offer("c");
        queue.offer("d");
        queue.offer("e");
        
        // a b c d e
        for(String q : queue){
            System.out.println(q);
        }
        
        // b c d e
        queue.poll(); //返回第一个元素，并在队列中删除
        for(String q : queue){
            System.out.println(q);
        }
        
        // b c d e
        queue.element(); //返回第一个元素 
        for(String q : queue){
            System.out.println(q);
        }
        
        // b c d e
        queue.peek(); //返回第一个元素 
        for(String q : queue){
            System.out.println(q);
        }
    }
}
```

**offer，add 区别：**

一些队列有大小限制，因此如果想在一个满的队列中加入一个新项，多出的项就会被拒绝。

这时新的 offer 方法就可以起作用了。它不是像调用 add() 方法抛出一个 unchecked 异常，而只是得到由 offer() 返回的 false。

**poll，remove 区别：**

remove() 和 poll() 方法都是从队列中删除第一个元素。remove() 的行为与 Collection 接口的版本相似， 但是新的 poll() 方法在用空集合调用时不是抛出异常，只是返回 null。因此新的方法更适合容易出现异常条件的情况。

**peek，element区别：**

element() 和 peek() 用于在队列的头部查询元素。与 remove() 方法类似，在队列为空时， element() 抛出一个异常，而 peek() 返回 null。
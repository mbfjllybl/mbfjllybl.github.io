---
title: <sgg视频学习>Stream API
date: 2021-11-11 10:38:33
updated:
tags: Stream
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

# Stream API

`java.util.stream`

+ Stream关注的是数据的运算，与CPU打交道
+ 集合关注的是数据的存储，与内存打交道
+ Stream自己不会存储数据
+ Stream不会改变源对象，他们会返回一个持有结果的新Stream
+ Stream操作是延迟执行的，这意味着他们会等到需要结果时才执行

执行流程

1. Stream实例化
2. 一系列中间操作（过滤，映射...）
3. 终止操作

### Stream实例化

#### 1. 通过集合

```java
@Test
public void test1() {
    List<Integer> list = new ArrayList<Integer>() {
        {
            for (int i = 1; i <= 10; i++)
                add(i);
        }
    };

    //返回顺序流
    //default Stream<E> stream()
    Stream<Integer> stream1 = list.stream();

    //返回并行流
    //default Stream<E> parallelStream()
    Stream<Integer> stream2 = list.parallelStream();

}
```

#### 2. 通过数组

如果是自定义类型，返回Stream

```java
@Test
public void test2() {
    int[] arr = {1, 2, 3};
    //Arrays中的static <T> Stream<T> stream<T[] array)
    IntStream stream1 = Arrays.stream(arr);
}
```

#### 3. Stream中的Stream of

```
@Test
public void test3() {
    //Stream中的Stream of
    Stream<Integer> stream1 = Stream.of(1, 2, 3, 4, 5);
}
```

#### 4. 创建无限流

```java
@Test
public void test4() {
    //迭代
    //public static<T> Stream<T> iterate(final T seed, final UnaryOperator<T> f)

    Stream.iterate(0, t -> t + 2).limit(10).forEach(System.out::println);

    //生成
    //public static<T> Stream<T> generate(Supplier<T> s)
    Stream.generate(Math::random).limit(10).forEach(System.out::println);
}
```

 ### Stream的中间操作

#### 1. 筛选与切片

```java
@Test
public void test1() {
    List<Integer> list = new ArrayList<Integer>() {
        {
            for (int i = 1; i <= 10; i++)
                add(i);
        }
    };
    //filter(Predicate p) 接受lambda,从流中排除某些数据
    Stream<Integer> stream1 = list.stream();
    stream1.filter(i -> i > 2).forEach(System.out::println);

    //limit(n) 截断流，使其元素不超过给定数量
    list.stream().limit(3).forEach(System.out::println);

    //skip(n) 跳过元素，返回一个扔了前n个元素的流，如果不足n,则返回空流
    list.stream().skip(3).forEach(System.out::println);

    //distinct() 筛选，通过流所生成元素的hashCode()与equals()去重
    list.stream().distinct().forEach(System.out::println);
}
```

#### 2. 映射

 ```java
 @Test
 public void test() {
     List<String> list = Arrays.asList("aa", "bb", "cc");
     //map(Function f) 接收一个函数作为参数，每个元素通过函数映射成一个新的元素
     list.stream().map(str -> str.toUpperCase()).forEach(System.out::println);
 
     list.stream().map(StreamTest3::toChar).forEach(stream -> stream.forEach(System.out::println));
     //flatMap(Function f) 接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流
     list.stream().flatMap(StreamTest3::toChar).forEach(System.out::println);
 }
 
 public static Stream<Character> toChar(String s) {
     List<Character> list = new ArrayList<>();
     for (Character c : s.toCharArray()) {
         list.add(c);
     }
     return list.stream();
 }
 ```

#### 3. 排序

```java
@Test
public void test2() {
    List<Integer> integers = Arrays.asList(3, 2, 1, 2, 3);
    //自然排序
    integers.stream().sorted().forEach(System.out::println);
    //定制排序
    integers.stream().sorted(Integer::compare).forEach(System.out::println);
}
```

### Stream终止操作

#### 1. 匹配与查找

```java
package T5.stream;

import org.junit.Test;

import java.util.ArrayList;
import java.util.List;

public class StreamTest4 {
    List<Integer> list = new ArrayList<Integer>() {
        {
            for (int i = 1; i <= 10; i++)
                add(i);
        }
    };
    @Test
    public void test1() {
        //allMatch 全部匹配
        boolean b1 = list.stream().allMatch(e -> e > 0);
        System.out.println(b1);

        //anyMatch 一个匹配
        boolean b2 = list.stream().anyMatch(e -> e < 0);
        System.out.println(b2);

        //noneMatch 检查是否没有匹配的元素
        boolean b3 = list.stream().noneMatch(e -> e < 0);
        System.out.println(b3);

        //findFirst 第一个元素Optional[1]
        System.out.println(list.stream().findFirst());

        //findAny 第随便一个元素
        System.out.println(list.stream().findAny());

        //count 个数
        System.out.println(list.stream().count());

        //max
        System.out.println(list.stream().max(Integer::compare));

        //min
        System.out.println(list.stream().min(Integer::compare));

        //forEach
        list.stream().forEach(System.out::println);
    }
}
```

#### 2. 归约

```java
package T5.stream;

import org.junit.Test;

import java.util.ArrayList;
import java.util.List;

public class StreamTest4 {
    List<Integer> list = new ArrayList<Integer>() {
        {
            for (int i = 1; i <= 10; i++)
                add(i);
        }
    };

    @Test
    public void test2() {
        // public abstract T reduce(T t, java.util.function.BinaryOperator<T> binaryOperator)
        // reduce 可以将流中的元素反复结合起来，得到一个值，返回
        System.out.println(list.stream().reduce(0, Integer::sum));

        System.out.println(list.stream().reduce(0, (o1, o2) -> o1 + o2));
    }
}
```

#### 3. 收集

```java
package T5.stream;

import org.junit.Test;

import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

public class StreamTest4 {
    List<Integer> list = new ArrayList<Integer>() {
        {
            for (int i = 1; i <= 10; i++)
                add(i);
        }
    };

    @Test
    public void test3() {
        list.stream().collect(Collectors.toList()).forEach(System.out::println);
    }
}
```


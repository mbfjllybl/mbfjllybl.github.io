---
title: <sgg视频学习>Java Lambda表达式&方法引用&构造器引用
date: 2021-11-11 00:38:33
updated:
tags: Lambda
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

# Java Lambda表达式&方法引用&构造器引用

### Lambda表达式的使用

举例：`(o1, o2) -> Integer.compare(o1, o2);`

格式：

+ `->`：lambda操作符或箭头操作符
+ 左边：lambda形参列表，其实就是接口中的抽象方法的形参列表
+ 右边：lambda体，其实就是重写的抽象方法的方法体

lambda表达式的本质：作为接口的一个实例

lambda表达式的使用，分为6种情况介绍：

#### 1. 无参且无返回值

`Runnable r1 = () -> System.out.println("QAQ");`

#### 2. 一个参数且没有返回值

```java
package T2.lambda;

import java.util.function.Consumer;

public class ConsumerTest {
    public static void main(String[] args) {
        Consumer<String> con1 = new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        };
        con1.accept("QAQ");
        Consumer<String> con2 = (String s) -> {
            System.out.println(s);
        };
        con2.accept("FUCK");
    }
}
```

#### 3. 省略数据类型

数据类型可以省略，因为可以由编译器推断得出，成为类型推断

```java
package T2.lambda;

import java.util.function.Consumer;

public class ConsumerTest {
    public static void main(String[] args) {
        Consumer<String> con1 = new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        };
        con1.accept("QAQ");
        Consumer<String> con2 = (s) -> {
            System.out.println(s);
        };
        con2.accept("FUCK");
    }
}
```

`ArrayList<String> list = new ArrayList<>();`类型推断

`int[] arr = {1, 2, 3};`类型推断

#### 4. 若只有一个参数则可省略小括号

```java
package T2.lambda;

import java.util.function.Consumer;

public class ConsumerTest {
    public static void main(String[] args) {
        Consumer<String> con1 = new Consumer<String>() {
            @Override
            public void accept(String s) {
                System.out.println(s);
            }
        };
        con1.accept("QAQ");
        Consumer<String> con2 = s -> {
            System.out.println(s);
        };
        con2.accept("FUCK");
    }
}
```

#### 5. 两个以上参数与执行语句且存在返回值

```java
package T2.lambda;

import java.util.Comparator;

public class ComparatorTest {
    public static void main(String[] args) {
        Comparator<Integer> com1 = new Comparator<Integer>() {
            @Override
            public int compare(Integer integer, Integer t1) {
                System.out.println("emmmm");
                System.out.println("QAQ");
                return Integer.compare(integer, t1);
            }
        };

        Comparator<Integer> com2 = (o1, o2) -> {
            System.out.println("111");
            System.out.println("222");
            return Integer.compare(o1, o2);
        };
        System.out.println(com1.compare(2, 1));
        System.out.println(com2.compare(1, 2));
    }
}
```

#### 6. 若仅一条执行语句则return与大括号若有则可省略

```java
package T2.lambda;

import java.util.Comparator;

public class ComparatorTest {
    public static void main(String[] args) {
        Comparator<Integer> com1 = new Comparator<Integer>() {
            @Override
            public int compare(Integer integer, Integer t1) {
                System.out.println("emmmm");
                System.out.println("QAQ");
                return Integer.compare(integer, t1);
            }
        };

        Comparator<Integer> com2 = (o1, o2) -> Integer.compare(o1, o2);

        System.out.println(com1.compare(2, 1));
        System.out.println(com2.compare(1, 2));
    }
}
```

---

总结：

左边：lambda形参列表的参数类型可以省略，若只有一个参数，则括号也可以省略

右边：如果只有一条语句，可以省略大括号，如果为return语句则return也可以省略

接口：为函数式接口（只有一个抽象方法）`@FunctionalInterface`

**java.util.function**

+ `Consumer<T>` 消费型接口 `void accept(T t)`

+ `Supplier<T>` 供给型接口 `T get()`
+ `Function<T, R>` 函数型接口 `R apply(T t)` 第一个参数是形参，第二个参数是返回类型
+ `Predicate<T>` 断定型接口 `boolean test(T t)`

+ ...

### 方法引用

当要传递给lambda体的操作，已经有实现的方法了，可以使用方法引用。

**接口中形参列表与返回值的类型与方法引用的形参列表和返回值类型相同。（针对于情况1、情况2）**

**第一个参数作为方法的调用者（情况3）**

格式：`类(对象)::方法名`，分为3种情况介绍：

+ `对象::非静态方法`

+ `类::静态方法`

+ `类::非静态方法`

#### 1. 对象::非静态方法

User类

```java
package T3;

public class User {
    public String name;

    public User() {
    }

    public User(String name) {
        this.name = name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

测试

```java
package T3;

import org.junit.Test;

import java.io.PrintStream;
import java.util.function.Consumer;
import java.util.function.Supplier;

public class lambdaByMethodQuote1 {
    @Test
    public void test1() {
        Consumer<String> con1 = str -> System.out.println(str);

        PrintStream ps = System.out;
        Consumer<String> con2 = ps::println;

        con1.accept("QAQ");
        con2.accept("fujang");
    }

    @Test
    public void test2() {
        User user = new User("mbfjllybl");
        Supplier<String> sup1 = () -> user.getName();
        System.out.println(sup1.get());
        Supplier<String> sup2 = user::getName;
        System.out.println(sup2.get());
    }
}
```

#### 2. 类::静态方法

```java
package T3;

import org.junit.Test;

import java.util.Comparator;
import java.util.function.Function;

public class lambdaByMethodQuote2 {
    @Test
    public void test1() {
        Comparator<Integer> com1 = (o1, o2) -> Integer.compare(o1, o2);
        System.out.println(com1.compare(1, 2));
        Comparator<Integer> com2 = Integer::compare;
        System.out.println(com2.compare(1, 2));
    }

    @Test
    public void test2() {
        Function<Double, Long> func1 = d -> Math.round(d);
        System.out.println(func1.apply(1.1));
        Function<Double, Long> func2 = Math::round;
        System.out.println(func2.apply(1.1));
    }
}
```

#### 3. 类::非静态方法

第一个参数作为方法的调用者，第一个参数其实就是this

```java
package T3;

import org.junit.Test;

import java.util.Comparator;
import java.util.function.BiPredicate;
import java.util.function.Function;

public class lambdaByMethodQuote3 {
    // Comparator 中 int compare(T t1, T t2)
    // String 中 int t1.compareTo(t2)
    @Test
    public void test1() {
        Comparator<String> com1 = (o1, o2) -> o1.compareTo(o2);
        System.out.println(com1.compare("abc", "abc"));

        Comparator<String> com2 = String::compareTo;
        System.out.println(com2.compare("abc", "abd"));
    }

    @Test
    public void test2() {
        BiPredicate<String, String> pre1 = (s1, s2) -> s1.equals(s2);
        System.out.println(pre1.test("abc", "abc"));

        BiPredicate<String, String> pre2 = String::equals;
        System.out.println(pre2.test("abd", "abc"));
    }

    @Test
    public void test3() {
        User user = new User("fujang");
        Function<User, String> fun1 = u -> u.getName();
        System.out.println(fun1.apply(user));

        Function<User, String> fun2 = User::getName;
        System.out.println(fun2.apply(user));
    }
}
```

### 构造器引用

和方法引用类似，接口中的形参列表和构造器中的形参列表一致。

抽象方法的返回值即为构造器所属的类。

```java
package T4;
import T3.User;
import org.junit.Test;

import java.util.function.Function;
import java.util.function.Supplier;

public class lambdaByConstructQuote1 {
    @Test
    public void test1() {
        Supplier<User> sup1 = () -> new User();
        System.out.println(sup1.get());

        Supplier<User> sup2 = User::new;
        System.out.println(sup2.get());
    }

    @Test
    public void test2() {
        Function<String, User> fun1 = s -> new User(s);
        System.out.println(fun1.apply("mbfjllybl"));

        Function<String, User> fun2 = User::new;
        System.out.println(fun2.apply("fujang"));
    }
}
```

### 数组引用

可以把数组看作一个特殊的类，写法和构造器引用一致。

```java
package T4;

import org.junit.Test;

import java.util.function.Function;

public class lambdaByArrayQuote1 {
    @Test
    public void test1() {
        //第一个参数是形参，第二个参数是返回类型
        Function<Integer, String[]> fun1 = len -> new String[len];
        System.out.println(fun1.apply(10));

        Function<Integer, String[]> fun2 = String[]::new;
        System.out.println(fun2.apply(10));
    }
}
```


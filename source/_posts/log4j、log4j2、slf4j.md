---
title: log4j、log4j2、slf4j
date: 2021-11-25 20:38:33
updated:
tags: 日志
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

# log4j、log4j2、slf4j

最近公司项目系统需要将日志从log4j+slf4j升级为log4j2，然后彻彻底底的把它们研究了一遍，在网上查找相关资源，发现并没有一篇文章能够很完整的把它们之间的关联和区别写出来，所以我在这里做一个总结。

### log4j

如果在我们系统中单独使用log4j的话，我们只需要引入log4j的核心包就可以了，我这里用的是：`log4j-1.2.17.jar`，然后在系统中使用如下代码输出日志：

```java
public class Log4jTest {
	private static final org.apache.log4j.Logger logger = org.apache.log4j.Logger.getLogger(Log4jTest.class);
	
	public static void main(String[] args) {
		logger.info("hello word");
	}
}
```

在系统的src目录下添加依赖的配置文件`log4j.properties`：

```xml
log4j.rootLogger=INFO, stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=System.out
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L - %m%n
```

控制台输出如下：

```shell
20:39:41,056  INFO Log4jTest:7 - hello word
```

#### log4j2

如果在我们系统中单独使用log4j2的话，我们只需要引入log4j2的核心包就可以了，我这里用的是：`log4j-api-2.7.jar`和`log4j-core-2.7.jar`，然后在系统中使用如下代码输出日志：

```java
public class Log4j2Test {
	private static org.apache.logging.log4j.Logger logger = org.apache.logging.log4j.LogManager.getLogger(Log4jTest.class);
	public static void main(String[] args) {
		logger.info("hello word");
	}
}
```

在系统的src目录下添加依赖的配置文件`log4j2.xml`：

```html
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="info">
	<Appenders>
		<Console name="consoleAppender" target="SYSTEM_OUT">
			<PatternLayout pattern="%d{DATE} %5p %c{1}:%L - %m%n" />
		</Console>
	</Appenders>
	<Loggers>
		<Root level="info">
			<AppenderRef ref="consoleAppender" />
		</Root>
	</Loggers>
</Configuration>
```

控制台输出如下：

```shell
25 11月 2021 20:47:47,404  INFO Log4j2Test:6 - hello word
```

关于log4j2的官方文档介绍，请查看：http://logging.apache.org/log4j/2.x/index.html
关于log4j2相关配置文件，大家可以从官方文档中了解，也可以参考：http://java12345678.iteye.com/blog/2382929
**log4j与log4j2的区别：**

1. 获取Logger的api不一样，log4j的api为`org.apache.log4j.Logger`，而log4j2的api为`org.apache.logging.log4j.Logger`
2. 配置方式不一样，log4j2对properties的配置支持不是很好，它的格式一般为xml格式或者yaml格式，这种格式的可读性比较好，各种配置一目了然
3. Log4j2.0基于LMAX Disruptor的异步日志在多线程环境下性能会远远优于Log4j 1.x和logback（官方数据是10倍以上）

### **slf4j**

它仅仅是一个为Java程序提供日志输出的统一接口，并不是一个具体的日志实现方案，就比如JDBC一样，只是一种规则而已，所以单独的slf4j是不能工作的，必须搭配其他具体的日志实现方案，比如log4j或者log4j2，它的优势和原理可以参考：https://blog.csdn.net/winwill2012/article/details/71786004，要在系统中使用slf4j，我们需要引入的核心包为：`slf4j-api-1.6.4.jar`。

### slf4j+log4j

如果我们在系统中需要使用slf4j和log4j来进行日志输出的话，我们需要引入下面的jar包：

+ log4j核心jar包：`log4j-1.2.17.jar`
+ slf4j核心jar包：`slf4j-api-1.6.4.jar`
+ slf4j与log4j的桥接包：`slf4j-log4j12-1.6.1.jar`，这个包的作用就是使用slf4j的api，但是底层实现是基于log4j

所以我们获取日志可以通过下面的代码进行：

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
 
 
public class Slf4jTest {
	private static final Logger logger = LoggerFactory.getLogger(Slf4jTest2.class);
 
 
	public static void main(String[] args) {
		logger.info("hello world");
	}
}
```

### slf4j+log4j2

如果我们在系统中需要使用slf4j和log4j2来进行日志输出的话，我们需要引入下面的jar包：

+ log4j2核心jar包：`log4j-api-2.7.jar`和`log4j-core-2.7.jar`
+ slf4j核心jar包：`slf4j-api-1.6.4.jar`
+ slf4j与log4j2的桥接包：`log4j-slf4j-impl-2.7.jar`，这个包的作用就是使用slf4j的api，但是底层实现是基于log4j2

所以我们获取日志仍可以通过下面的代码进行：

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
 
 
public class Slf4jTest {
	private static final Logger logger = LoggerFactory.getLogger(Slf4jTest2.class);
 
 
	public static void main(String[] args) {
		logger.info("hello world");
	}
}
```

### slf4j+log4j不修改代码升级到log4j2

如果我们系统中刚开始用的是slf4j和log4j，然后出于性能考虑，要升级到slf4j和log4j2，并且不需要改动任何代码的话(因为我们系统可能是一个大工程，然后基本上每个类都会有日志输出，改动代码可能牵一发而动全身)，出于这个考虑，我们可以这样来进行修改：

1. 删除项目中存在的Log4j1.x所必须的log4j和slf4j-log4j12等依赖，例如从我们上面做的去升级的话，需要删除`log4j-1.2.17.jar`和`slf4j-log4j12-1.6.1.jar`
2. 添加log4j2和slf4j桥接包：`log4j-slf4j-impl-2.7.jar`替换log4j和slf4j桥接包：`slf4j-log4j12-1.6.1.jar`
3. 如果我们在系统中使用了log4j的api去获取Logger的话：`org.apache.log4j.Logger logger = org.apache.log4j.Logger.getLogger(Log4jTest.class)`，我们需要添加`log4j-1.2-api-2.7.jar`去替换`log4j-1.2.17.jar`
4. 将log4j的properties文件修改为log4j2的xml文件，然后，同样在系统中使用slf4j的方式获取日志：`org.slf4j.Logger logger = org.slf4j.LoggerFactory.getLogger(Self4jTest.class);`

> [link](https://blog.csdn.net/HarderXin/article/details/80422903)
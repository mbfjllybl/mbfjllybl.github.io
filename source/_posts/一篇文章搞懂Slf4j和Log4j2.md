---
title: 一篇文章搞懂Slf4j和Log4j2
date: 2021-11-25 21:03:33
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

# 一篇文章搞懂Slf4j和Log4j2

### 前言

一如既往的上班，一如既往的打开IDEA，一如既往的写下了这样的一行代码：

```java
private static final Logger log = LoggerFactory.getLogger(CommonController.class);
```

等等，写了这么久了，只知道这样可以很方便的使用log对象记录日志，那这到底是如何记录的呢？那么今天就来对这行代码背后使用的Slf4j和Log4j2一探究竟。

### 关于Slf4j和Log4j2

说起Slf4j和Log4j2，我们都知道这个是记录日志的，但是在Java的世界中，记录日志的框架那么多，那么这么多日志框架中，我们是如何相中Slf4j和Log4j2的？Slf4j和Log4j2和又是什么关系呢？好的，收起我们的疑问，你将在这篇文章中，一扫你的疑惑，满足你对Slf4j和Log4j2的一切想象。

首先，你要知道，Slf4j是一个日志框架，它是对所有日志框架制定的一种规范、标准、接口，并不是一个框架的具体的实现，因为接口并不能独立使用，它需要和具体的日志框架来配合使用；

其次，你要知道，Log4j2是一个日志实现，它是一种记录日志的具体实现；在Java中具体的日志实现有好多中，比如Log4j、Log4j2、Slf4j、JDKLog、Logback等等；

最后，你要知道，框架和实现的这种搭配使用方式，是为了以后如果项目对日志有其它要求而需要更换日志框架时可以不改动代码，只需要把依赖的jar包换掉就可以了。

在上面提到的日志框架中，以Slf4j和Log4j2的使用组合最为常见，但是我们知道Log4j目前已经停止更新了。Apache推出了新的Log4j2来代替Log4j，Log4j2是对Log4j的升级，与其前身Log4j相比有了显着的改进，并提供了许多Logback可用的改进，同时解决了Logback体系结构中的一些固有问题。因此，Slf4j和Log4j2应该是未来的大势所趋。

### 与Spring Boot集成

在普通的Maven项目中，需要使用Slf4j和Log4j2时，添加以下Maven依赖即可：

```xml
<!--log4j2核心包-->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.9.1</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.9.1</version>
</dependency>
<!--用于与slf4j保持桥接-->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.11.0</version>
</dependency>
<!-- slf4j核心包-->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.25</version>
</dependency>
```

平时工作中使用Spring Boot比较多，所以这里着重总结一下Spring Boot与log4j2的集成。Spring Boot默认使用`logback`，但相比较而言，log4j2在性能上面会更好。log4j2在使用方面与log4j基本上没什么区别，比较大的区别是log4j2不再支持properties配置文件，支持xml、json格式的文件。

### Log4j2配置说明

Log4j2的与Spring Boot的集成和使用都没有什么难点，而对于Log4j2的配置来说，则是整个的精华所在，也是整个的难点所在。所以这里有必要对Log4j2的配置进行详细的总结说明。

Log4j2日志有以下日志级别，从小到大依次为：`trace`、`debug`、`info`、`warn`和`error`，级别越大，输出的日志信息越少。接下来说说配置文件的结构：

```coq
+-Configuration
    +-Properties
    +-Appenders:
        +-Appender
            +-Layout
            +-Policies
            +-Strategy
    +-Loggers
        +-Logger
        +-RootLogger
```

根节点Configuration有两个属性：`status`和`monitorinterval`：

- `status`：用来指定log4j本身的打印日志的级别；
- `monitorinterval`：用于指定log4j2自动重新配置的监测间隔时间，单位是s，最小是5s。

节点说明：

- `Properties`：一般用来做属性定义；
- `Appender`：可以理解为一个管道，定义了日志内容的输出位置：
  - `Console`：用来定义输出到控制台Appender；
    - `name`：指定Appender的名字；
    - `target`：SYSTEM_OUT或SYSTEM_ERR，一般只设置默认：SYSTEM_OUT；
    - `PatternLayout`：输出格式，不设置默认为：%m%n；
    - `Filter`：配置日志事件能否被输出。过滤条件有三个值：ACCEPT(接受)，DENY(拒绝)，NEUTRAL(中立)；
  - `File`：用来定义输出到指定位置的文件Appender；
    - `name`：指定Appender的名字；
    - `fileName`：指定输出日志的目的文件带全路径的文件名；
    - `PatternLayout`：输出格式，不设置默认为：%m%n；
    - `Filter`：配置日志事件能否被输出。过滤条件有三个值：ACCEPT(接受)，DENY(拒绝)，NEUTRAL(中立)；
  - `RollingFile`：用来定义超过指定大小自动删除旧的创建新文件Appender；
    - `name`：指定Appender的名字；
    - `fileName`：指定输出日志的目的文件带全路径的文件名；
    - `PatternLayout`：输出格式，不设置默认为：%m%n；
    - `Filter`：配置日志事件能否被输出。过滤条件有三个值：ACCEPT(接受)，DENY(拒绝)，NEUTRAL(中立)；
    - `PatternLayout`：输出格式，不设置默认为：%m%n；
    - `Policies`：指定滚动日志的策略，就是什么时候进行新建日志文件输出日志；
    - `Strategy`：配置Strategy以控制日志如何(How)进行滚动。
- `Loggers`：简单说Logger就是一个路由器，指定类、包中的日志信息流向哪个Appender，以及控制他们的日志级别；
  - `Root`：必须要配置；用来指定项目的根日志，如果没有单独指定Logger，那么就会默认使用该Root日志输出；
    - `level`：属性；用来指定日志输出级别；
    - `AppenderRef`：`Root`的子节点，用来指定该日志输出到哪个`Appender`。
  - `Logger`：用来单独指定日志的形式，比如要为指定包下的class指定不同的日志级别等。
    - `level`：属性；用来指定日志输出级别；
    - `name`：属性；用来指定该Logger所适用的类或者类所在的包全路径，继承自Root节点；
    - `AppenderRef`：Logger的子节点，用来指定该日志输出到哪个Appender；如果没有指定，就会默认继承自Root；如果指定了，那么会在指定的这个Appender和Root的Appender中都会输出，此时我们可以设置Logger的additivity="false"只在自定义的Appender中进行输出。

上面说到的`Filter`一般常用的有以下三种：

- `LevelRangeFilter`：输出指定日志级别范围之内的日志；
- `TimeFilter`：在指定时间范围内才会输出日志；
- `ThresholdFilter`：输出符合特定日志级别及其以上级别的日志。

上面说到的`Policies`一般常用的有以下三种：

- `SizeBasedTriggeringPolicy`：根据日志文件的大小进行滚动；单位有：KB，MB，GB；
- `CronTriggeringPolicy`：使用Cron表达式进行日志滚动，很灵活；
- `TimeBasedTriggeringPolicy`：这个配置需要和filePattern结合使用，注意filePattern中配置的文件重命名规则。滚动策略依赖于filePattern中配置的最具体的时间单位，根据最具体的时间单位进行滚动。这种方式比较简洁。

### 使用示例

为了方便大家参考学习，提供一个样例配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Configuration后面的status，这个用于设置log4j2自身内部的信息输出，可以不设置，当设置成trace时，你会看到log4j2内部各种详细输出-->
<!--monitorInterval：Log4j能够自动检测修改配置 文件和重新配置本身，设置间隔秒数-->
<configuration monitorInterval="5">
    <!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->

    <!--变量配置-->
    <Properties>
        <!-- 格式化输出：%date表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度 %msg：日志消息，%n是换行符-->
        <!-- %logger{36} 表示 Logger 名字最长36个字符 -->
        <property name="LOG_PATTERN" value="%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n" />
        <!-- 定义日志存储的路径，不要配置相对路径 -->
        <property name="FILE_PATH" value="E:\logs\log4j2" />
        <property name="FILE_NAME" value="springbootlog4j2" />
    </Properties>

    <appenders>

        <console name="Console" target="SYSTEM_OUT">
            <!--输出日志的格式-->
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <!--控制台只输出level及其以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <ThresholdFilter level="info" onMatch="ACCEPT" onMismatch="DENY"/>
        </console>

        <!--文件会打印出所有信息，这个log每次运行程序会自动清空，由append属性决定，适合临时测试用-->
        <File name="Filelog" fileName="${FILE_PATH}/test.log" append="false">
            <PatternLayout pattern="${LOG_PATTERN}"/>
        </File>

        <!-- 这个会打印出所有的info及以下级别的信息，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
        <RollingFile name="RollingFileInfo" fileName="${FILE_PATH}/info.log" filePattern="${FILE_PATH}/${FILE_NAME}-INFO-%d{yyyy-MM-dd}_%i.log.gz">
            <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <ThresholdFilter level="info" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <Policies>
                <!--interval属性用来指定多久滚动一次，默认是1 hour-->
                <TimeBasedTriggeringPolicy interval="1"/>
                <SizeBasedTriggeringPolicy size="10MB"/>
            </Policies>
            <!-- DefaultRolloverStrategy属性如不设置，则默认为最多同一文件夹下7个文件开始覆盖-->
            <DefaultRolloverStrategy max="15"/>
        </RollingFile>

        <!-- 这个会打印出所有的warn及以下级别的信息，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
        <RollingFile name="RollingFileWarn" fileName="${FILE_PATH}/warn.log" filePattern="${FILE_PATH}/${FILE_NAME}-WARN-%d{yyyy-MM-dd}_%i.log.gz">
            <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <ThresholdFilter level="warn" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <Policies>
                <!--interval属性用来指定多久滚动一次，默认是1 hour-->
                <TimeBasedTriggeringPolicy interval="1"/>
                <SizeBasedTriggeringPolicy size="10MB"/>
            </Policies>
            <!-- DefaultRolloverStrategy属性如不设置，则默认为最多同一文件夹下7个文件开始覆盖-->
            <DefaultRolloverStrategy max="15"/>
        </RollingFile>

        <!-- 这个会打印出所有的error及以下级别的信息，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
        <RollingFile name="RollingFileError" fileName="${FILE_PATH}/error.log" filePattern="${FILE_PATH}/${FILE_NAME}-ERROR-%d{yyyy-MM-dd}_%i.log.gz">
            <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <ThresholdFilter level="error" onMatch="ACCEPT" onMismatch="DENY"/>
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <Policies>
                <!--interval属性用来指定多久滚动一次，默认是1 hour-->
                <TimeBasedTriggeringPolicy interval="1"/>
                <SizeBasedTriggeringPolicy size="10MB"/>
            </Policies>
            <!-- DefaultRolloverStrategy属性如不设置，则默认为最多同一文件夹下7个文件开始覆盖-->
            <DefaultRolloverStrategy max="15"/>
        </RollingFile>

    </appenders>

    <!--Logger节点用来单独指定日志的形式，比如要为指定包下的class指定不同的日志级别等。-->
    <!--然后定义loggers，只有定义了logger并引入的appender，appender才会生效-->
    <loggers>

        <!--过滤掉spring和mybatis的一些无用的DEBUG信息-->
        <logger name="org.mybatis" level="info" additivity="false">
            <AppenderRef ref="Console"/>
        </logger>
        <!--监控系统信息-->
        <!--若是additivity设为false，则 子Logger 只会在自己的appender里输出，而不会在 父Logger 的appender里输出。-->
        <Logger name="org.springframework" level="info" additivity="false">
            <AppenderRef ref="Console"/>
        </Logger>

        <root level="info">
            <appender-ref ref="Console"/>
            <appender-ref ref="Filelog"/>
            <appender-ref ref="RollingFileInfo"/>
            <appender-ref ref="RollingFileWarn"/>
            <appender-ref ref="RollingFileError"/>
        </root>
    </loggers>
</configuration>
```

### 总结

如果每天学习一个知识点，那么一年就是365个，加油吧，少年。

> [link](https://segmentfault.com/a/1190000037598528)
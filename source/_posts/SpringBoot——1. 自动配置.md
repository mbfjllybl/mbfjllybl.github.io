---
title: <ks视频学习>SpringBoot——1. 自动配置
date: 2021-11-07 17:38:33
updated:
tags: SpringBoot
categories: SpringBoot
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

## SpringBoot——1. 自动配置

#### pom.xml

+ spring-boot-dependencies：核心依赖在父工程中
+ 我们在写入或者引入一些springboot依赖时，不需要指定版本，就是因为有这些版本仓库

#### 启动器

+ springboot的启动场景
+ 比如spring-boot-starter-web
+ springboot将所有的功能场景，都变成一个个的起动器，如果我们要使用什么功能，直接添加对应的启动器即可

#### 主程序

@SpringBootApplication：标注此类为springboot的应用，启动类下的所有资源被导入

```java
@SpringBootConfiguration->@Configuration->@Component
@EnableAutoConfiguration->@AutoConfigurationPackage->@Import({Registrar.class})//自动注册包
    					->@Import({AutoConfigurationImportSelector.class})/*自动导入包*/->List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);
```

获取候选的配置

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```

`/home/mbfjllybl/.m2/repository/org/springframework/boot/spring-boot-autoconfigure/2.5.6/spring-boot-autoconfigure-2.5.6.jar!/META-INF/spring.factories`

springboot所有的自动配置都是在启动的时候扫描并加载，所有的配置都在spring.factories里，但是不一定生效，只有导入对应的启动器才生效。

#### run方法

+ 推断应用的类型是普通的项目还是web项目
+ 查找并加载所有可用的初始化器，设置到initializers属性中
+ 找出所有的应用程序监听器，设置到listeners属性中
+ 推断并设置main方法的定义类，找到运行的主类

#### application.yaml/application.properties

`ConfigurationProperties(prefix = "person")`

`PropertySource(value = "classpath:QAQ.properties")`加载指定的配置文件

`@value("${name}")`

yaml中`age=${random.int}`

|                      | @ConfigurationProperties | @Value     |
| -------------------- | ------------------------ | ---------- |
| 功能                 | 批量注入配置文件中的属性 | 一个个指定 |
| 松散绑定（松散语法） | 支持                     | 不支持     |
| SpEL                 | 不支持                   | 支持       |
| JSR303数据校验       | 支持                     | 不支持     |
| 复杂类型封装         | 支持                     | 不支持     |

松散绑定 lastName last-name

JSR303数据校验 `@Validated`放在类上 `@Email()`放在属性上

#### 配置文件位置

优先级排序如下：从大到小

+ `file:./config/application.yaml`
+ `file:./`
+ `classpath:/config/`
+ `classpath:/`

#### 多环境配置文件

`application-dev.properties`

`spring.profiles.active=dev`

```yaml
server:
  port: 8080

spring:
  profiles:
    active: dev
---
server:
  port: 8081
spring:
  profiles: dev
```

#### 配置文件内容与spring.factories的联系

```java
//表示这设计一个配置类
@Configuration(
    proxyBeanMethods = false
)

//自动配置属性ServerProperties
@EnableConfigurationProperties({ServerProperties.class})

//spring底层注释：根据不同的条件来判断当前配置或者类是否生效
@ConditionalOnWebApplication(
    type = Type.SERVLET
)
@ConditionalOnClass({CharacterEncodingFilter.class})
@ConditionalOnProperty(
    prefix = "server.servlet.encoding",
    value = {"enabled"},
    matchIfMissing = true
)
public class HttpEncodingAutoConfiguration { }
```

`ServerProperties`中

```java
@ConfigurationProperties(
    prefix = "server",
    ignoreUnknownFields = true
)
public class ServerProperties { }
```

#### 自动装配的原理

+ springboot启动会加载大量的自动配置类
+ 我们看我们需要的功能有没有在springboot默认写好的自动配置类当中
+ 我们再来看这个自动配置类中到底配置了哪些组件（只要我们要用的组件存在其中，我们就不需要再手动配置了）
+ 给容器中自动配置类添加组件的时候，会从properties类中获取某些属性，我们只需要在配置文件中指定这些属性的值即可

`xxxxAutoConfiguration`自动配置类 给容器中添加组件

`xxxProperties`封装配置文件中相关属性

#### 检测`debug: true`查看哪些自动配置类生效

`postive matches`生效

`negative matches`未生效

...


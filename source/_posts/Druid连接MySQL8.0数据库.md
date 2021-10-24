---
title: Druid连接MySQL8.0数据库
date: 2021-10-23 23:38:33
updated:
tags: MySQL
categories:
- [MySQL]
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

 ## 导入jar包

``mysql-connector-java-8.0.11.jar``

``druid-1.1.10.jar``

## properties文件内容

```properties
prop.driverClass=com.mysql.cj.jdbc.Driver
prop.url=jdbc:mysql://localhost:3306/库名?serverTimezone=UTC&amp
prop.userName=帐号
prop.password=密码
```




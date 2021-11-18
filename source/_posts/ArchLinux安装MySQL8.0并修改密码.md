---
title: ArchLinux安装MySQL8.0并修改密码
date: 2021-10-24 12:38:33
updated:
tags: MySQL
categories:
- [MySQL]
- [Linux]
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

## 第一步： 更新源

更新软件仓库

``sudo pacman -Syy``

查看软件仓库MySQL版本

``pacman -Si mysql``

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/2.jpg)

## 第二步： 安装 MySQL

安装命令如下

``sudo pacman -S mysql``

安装成功之后， 会看到如下提示

```bash
:: You need to initialize the MySQL data directory prior to starting
   the service. This can be done with mysqld --initialize command, e.g.:
   mysqld --initialize --user=mysql --basedir=/usr --datadir=/var/lib/mysql
:: Additionally you should secure your MySQL installation using
   mysql_secure_installation command after starting the mysqld service
```

## 第三步： 初始化

``sudo mysqld --initialize --user=mysql --basedir=/usr --datadir=/var/lib/mysql``

会生成对应的用户名与密码。

## 第四步： 开机自启

``sudo systemctl enable mysqld.service``

## 第五步： 启动 MySQL 服务

``sudo systemctl start mysqld.service``

``systemctl status mysqld.service``

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/photo_2021-10-24_16-11-08.jpg)

## 第六步：连接数据库

``mysql -uroot -p``

## 第七步：更改密码

``ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';``

## 参考文章

> [link](https://blog.csdn.net/Exception_sir/article/details/82111014)
>
> [link](https://blog.csdn.net/qq_40829735/article/details/81166669)

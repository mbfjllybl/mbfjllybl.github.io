---
title: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead. 
date: 2021-10-23 20:28:31
updated:
tags: Git
categories:
- [Git]
- [ERROR summary]
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

## 问题

> remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead. 
> remote: Please see https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/ for more information. 
> fatal: 'https://github.com/mbfjllybl/mbfjllybl.github.io.git/' 鉴权失败

今天提交代码，push到Github上，出现了这个问题。

大致意思是，密码验证不再支持，也就是今天不能再用密码方式去提交代码。请用使用**personal access token**替代。

## Github配置SSH免密登录

在Arch下``~/.ssh``	存在如下：

-  ``id_rsa ``（私钥）——这个不能泄露
-  ``id_rsa.pub``（公钥）

如果没有配过，则用此命令``ssh-keygen -t rsa -C "你自己注册GitHub的邮箱" ``，``ssh-keygen``的命令在``openssh``包里。

接着会提示这个公钥私钥的保存路径-建议直接回车就好（默认目录里)。接着提示输入私钥密码，如果不想使用私钥登录的话，私钥密码为空，直接回车。

生成成功后，把``id_rsa.pub``拷贝到Github新建的``SSH keys``中。

``ssh -T git@github.com``测试登陆

记住，你项目得使用``SSH clone``。

如果本地是``https``源，那么就修改``git``仓库地址。

## 修改Git仓库地址

直接修改``config``文件：``.git``文件夹，找到``config``，编辑把旧的项目地址替换成新的。

## Intellij IDEA 上Github账号校验

选择``token``登陆，在Github上配置``token``。

## 参考文章

> [link](https://cloud.tencent.com/developer/article/1861466)
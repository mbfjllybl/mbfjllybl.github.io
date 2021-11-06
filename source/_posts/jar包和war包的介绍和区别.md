---
title: jar包和war包的介绍和区别
date: 2021-11-06 10:38:33
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

# jar包和war包的介绍和区别

JavaSE程序可以打包成Jar包(J其实可以理解为Java了)，而JavaWeb程序可以打包成war包(w其实可以理解为Web了)。然后把war发布到Tomcat的webapps目录下，Tomcat会在启动时自动解压war包。

JAR（Java Archive，Java 归档文件）是与平台无关的文件格式，它允许将许多文件组合成一个压缩文件。为 J2EE 应用程序创建的 JAR 文件是 EAR 文件（企业 JAR 文件）。

JAR 文件格式以流行的 ZIP 文件格式为基础。与 ZIP 文件不同的是，JAR 文件不仅用于压缩和发布，而且还用于部署和封装库、组件和插件程序，并可被像编译器和 JVM 这样的工具直接使用。在 JAR 中包含特殊的文件，如 manifests 和部署描述符，用来指示工具如何处理特定的 JAR。

---

如果一个Web应用程序的目录和文件非常多，那么将这个Web应用程序部署到另一台机器上，就不是很方便了，我们可以将Web应用程序打包成Web 归档（WAR）文件，这个过程和把Java类文件打包成JAR文件的过程类似。利用WAR文件，可以把Servlet类文件和相关的资源集中在一起进行发布。在这个过程中，Web应用程序就不是按照目录层次结构来进行部署了，而是把WAR文件作为部署单元来使用。

一个WAR文件就是一个Web应用程序，建立WAR文件，就是把整个Web应用程序（不包括Web应用程序层次结构的根目录）压缩起来，指定一个.war扩展名。

---

要注意的是，虽然WAR文件和JAR文件的文件格式是一样的，并且都是使用jar命令来创建，但就其应用来说，WAR文件和JAR文件是有根本区别的。JAR文件的目的是把类和相关的资源封装到压缩的归档文件中，而对于WAR文件来说，一个WAR文件代表了一个Web应用程序，它可以包含 Servlet、HTML页面、Java类、图像文件，以及组成Web应用程序的其他资源，而不仅仅是类的归档文件。

我们什么时候应该使用WAR文件呢？在开发阶段不适合使用WAR文件，因为在开发阶段，经常需要添加或删除Web应用程序的内容，更新 Servlet类文件，而每一次改动后，重新建立WAR文件将是一件浪费时间的事情。在产品发布阶段，使用WAR文件是比较合适的，因为在这个时候，几乎不需要再做什么改动了。

在开发阶段，我们通常将Servlet源文件放到Web应用程序目录的src子目录下，以便和Web资源文件区分。在建立WAR文件时，只需要将src目录从Web应用程序目录中移走，就可以打包了

> [link](https://blog.csdn.net/u012110719/article/details/44260417)

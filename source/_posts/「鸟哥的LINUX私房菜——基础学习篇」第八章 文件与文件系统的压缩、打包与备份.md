---
title: 「鸟哥的LINUX私房菜——基础学习篇」第八章 文件与文件系统的压缩、打包与备份
date: 2023-08-12 13:08:33
updated:
tags: LINUX
categories: LINUX
keywords: 
description:
top_img:
comments:
cover:
top: 100
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

# 第八章 文件与文件系统的压缩、打包与备份

## 8.1 压缩文件的用途与技术

## 8.2 Linux 系统常见的压缩指令

+ 常见的扩展名。

  ```
  *.Z compress 程序压缩的文件；
  *.zip zip 程序压缩的文件；
  *.gz gzip 程序压缩的文件；
  *.bz2 bzip2 程序压缩的文件；
  *.xz xz 程序压缩的文件；
  *.tar tar 程序打包的数据，并没有压缩过；
  *.tar.gz tar 程序打包的文件，其中并且经过 gzip 的压缩
  *.tar.bz2 tar 程序打包的文件，其中并且经过 bzip2 的压缩
  *.tar.xz tar 程序打包的文件，其中并且经过 xz 的压缩
  ```

### 8.2.1 gzip, zcat/zmore/zless/zgrep

+  zcat/zmore/zless 则可以对应于 cat/more/less 的方式来读取纯文本文件被压缩后的压缩文件！ 由于 gzip 这个压缩指令主要想要用来取代 compress 的，所以不但 compress 的压缩文件可以使用 gzip 来解开，同时 zcat 这个指令可以同时读取 compress 与 gzip 的压缩文件呦！ 另外，如果你还想要从文字压缩文件当中找数据的话，可以通过 zgrep 来搜寻关键字喔！而不需要将压缩文件解开才以 grep 进行！这对查询备份中的文本文件数据相当有用！

+ `gzip`压缩文件，`gzip -d`解压缩文件。

### 8.2.2 bzip2, bzcat/bzmore/bzless/bzgrep

+ 若说 gzip 是为了取代 compress 并提供更好的压缩比而成立的，那么 bzip2 则是为了取代 gzip 并提供更佳的压缩比而来的。 bzip2 真是很不错用的东西～这玩意的压缩比竟然比 gzip 还要好～至于 bzip2 的用法几乎与 gzip 相同！

### 8.2.3 xz, xzcat/xzmore/xzless/xzgrep

+ 虽然 xz 这个压缩比真的好太多太多了！但是时间花费更多，如果你并不觉得时间是你的成本考虑，那么使用 xz 会比较好！ 如果时间是你的重要成本，那么 gzip 恐怕是比较适合的压缩软件喔！

## 8.3 打包指令： tar

+ 虽然 gzip, bzip2, xz 也能够针对目录来进行压缩，不过， 这两个指令对目录的压缩指的是“将目录内的所有文件 "分别" 进行压 缩”的动作！tar 可以将多个目录或文件打包成一个大文件，同时还可以通过 gzip/bzip2/xz 的支持，将该文件同时进行压缩！
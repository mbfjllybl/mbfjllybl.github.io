---
title: Hexo MAC代码框样式美化
date: 2021-11-16 13:20:33
updated:
tags: Hexo
categories: Hexo
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

# Hexo MAC代码框样式美化

### 前言

本文将介绍如何美化MAC代码框。具体效果如下：

MAC白色样式：

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111161315229.png)

MAC黑色样式：

![](https://cdn.jsdelivr.net/gh/mbfjllybl/pictures-bed/202111161315933.png)

> [教程链接：Hexo博客之butterfly主题优雅魔改系列（持续更新）_小康博客-CSDN博客](https://blog.csdn.net/u012208219/article/details/106883001/)

### 正文

打开站点的主题配置文件`_config.butterfly.yml`，找到inject，在head处直接引入以下链接：

MAC黑色：

````yaml
- <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/sviptzk/StaticFile_HEXO@latest/butterfly/css/macblack.css">
````

MAC白色：

```yaml
- <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/sviptzk/StaticFile_HEXO@latest/butterfly/css/macWhite.css">
```

以上两种样式二选一，重新部署，即可看到效果。


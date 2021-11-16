---
title: Hexo插入响应式Bilibili视频代码块
date: 2021-11-16 12:20:33
updated:
tags: Hexo
categories:
- [Hexo配置与美化]
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

# Hexo插入响应式Bilibili视频代码块

### 前言

本文将介绍如何在Hexo页面中插入响应式Bilibili视频代码块。

> [教程链接：如何在Hugo/Hexo博客中插入响应式Bilibili视频代码块_知识星海-CSDN博客](https://blog.csdn.net/weixin_41216356/article/details/106911233)

### 操作

b站官方已经提供了iframe标签，有些小伙伴可能已经发现了。在网页打开b站视频页时，鼠标移至分享按钮，就可以复制。

只需要在页面中复制该代码即可。

但是为了在页面上获得更好的显示效果

我们可以在该标签中添加一个css样式，在标签外侧再嵌套一个div标签。

```html
<div style="position: relative; width: 100%; height: 0; padding-bottom: 75%;"> 
    <iframe src="//player.bilibili.com/player.html?aid=202642420&bvid=BV14a411c7KJ&cid=254702541&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> 
    </iframe>
</div>  
```

### 效果

<div style="position: relative; width: 100%; height: 455; padding-bottom: 75%;"> 
    <iframe src="//player.bilibili.com/player.html?aid=506303640&bvid=BV17u411o7Xk&cid=429933349&page=7" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" style="position:absolute; height: 100%; width: 100%;"> </iframe>
</div>  







 


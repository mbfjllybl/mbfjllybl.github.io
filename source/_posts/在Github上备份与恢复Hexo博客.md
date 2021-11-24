---
title: 在Github上备份Hexo博客
date: 2021-10-23 22:38:33
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

## 在Github上备份Hexo博客

在Github上你的``github.io``仓库中设置默认分支为``hexo``。这样有助于之后恢复博客。

``master``分支是默认的博客静态页面分支，在之后恢复博客的时候并不需要。

将博客仓库clone至本地，将之前的Hexo文件夹中的`_config.yml`，`themes/`，`source`，`scffolds/`，`package.json`，`.gitignore`复制到你克隆下来的仓库文件夹，即`username.github.io`。

个人而言习惯于先备份文件再生成博客。

+ ``git add .``

+ ``git commit -m "Backup"``

+ ``git push origin hexo``

+ ``hexo clean g -d``

## 恢复博客

+ ``git clone git@github.com:mbfjllybl/mbfjllybl.github.io.git``
+ ``npm install hexo-cli``
+ ``npm install``
+ ``npm install hexo-deployer-git``
+ ``npx hexo clean``
+ ``npx hexo g``
+ ``npx hexo d``


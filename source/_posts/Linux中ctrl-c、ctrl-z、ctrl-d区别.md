---
title: Linux中ctrl-c、ctrl-z、ctrl-d区别
date: 2021-11-11 14:38:33
updated:
tags: Linux
categories: Linux
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

# Linux中ctrl-c、ctrl-z、ctrl-d区别

`ctrl-c`：（kill foreground process）发送 SIGINT 信号给前台进程组中的所有进程，强制终止程序的执行

`ctrl-z`：（suspend foreground process）发送 SIGTSTP 信号给前台进程组中的所有进程，常用于挂起一个进程，而并非结束进程，用户可以使用使用`fg/bg`操作恢复执行前台或后台的进程。`fg`命令在前台恢复执行被挂起的进程，此时可以使用`ctrl-z`再次挂起该进程，`bg`命令在后台恢复执行被挂起的进程，而此时将无法使用`ctrl-z`再次挂起该进程；一个比较常用的功能： 正在使用vi编辑一个文件时，需要执行shell命令查询一些需要的信息，可以使用ctrl-z挂起vi，等执行完shell命令后再使用`fg`恢复vi继续编辑你的文件（当然，也可以在vi中使用`!command`方式执行shell命令，但是没有该方法方便）

`ctrl-d`：（Terminate input, or exit shell）一个特殊的二进制值，表示 EOF，作用相当于在终端中输入exit后回车

`ctrl-/`：发送 SIGQUIT 信号给前台进程组中的所有进程，终止前台进程并生成 core 文件

`ctrl-s`：中断控制台输出

`ctrl-q`：恢复控制台输出

`ctrl-l`：清屏


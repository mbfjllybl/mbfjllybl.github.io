---
title: xv6编写用户程序
date: 2021-10-26 9:38:33
updated:
tags: 6.S081
categories: 操作系统
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

# xv6编写用户程序

## 1. 编写代码

在 `xv6-riscv/user/` 里新建一个 `helloworld.c`，写一个 hello world：

```c
#include "kernel/types.h"
#include "kernel/stat.h"
#include "user/user.h"

int main() {
	printf("Hello World!\n");
	exit(0);
}
```

这个和平时我们在真实系统中写的代码有少许区别：

+ 导库：`kernel/types.h`、`kernel/stat.h`、`user/user.h`。你可以看到`xv6-riscv/user/*.c`头三行基本都是这么写的，咱们有样学样就可。
+ 不要`return 0;`，要`exit(0);`。这一点同样可以参考其他系统随附的程序得出。

## 2. 修改 Makefile

`Xv6`系统中没有编译器的实现，所以我们需要把程序在编译系统时一并编译。修改 `xv6-riscv/Makefile`，找到 `UPROGS` (大概118行)，保持格式，在后面添加注册新程序：

```c
UPROGS=\
	$U/_cat\
	$U/_echo\
	...
	$U/_helloworld\
```

编写的代码 `user/xxx.c`，对应这里写 `$U/_xxx\`。

## 3. 编译运行 Xv6

在 Xv6 中 `ls`，可以看到我们的`helloworld`程序：

```c
$ helloworld
Hello World!
```


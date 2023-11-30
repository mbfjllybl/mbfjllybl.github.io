---
title: ArchLinux KDE使用蓝牙音频设备
date: 2021-10-24 23:48:33
updated:
tags: Arch
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

# ArchLinux KDE使用蓝牙音频设备

## 安装过程

- 安装提供蓝牙的协议栈的``bluez``包。``sudo pacman -S bluez``

- 安装``bluez-utils``其提供`bluetoothctl`工具。``sudo pacman -S bluez-utils``

- 启动蓝牙服务。``sudo systemctl start bluetooth.service``，``sudo systemctl enable bluetooth.service``

- 要使用蓝牙音响以及蓝牙耳机需要安装``pulseaudio-bluetooth``。``sudo pacman -S pulseaudio-bluetooth``

- 同时建议安装``pavucontrol``。``sudo pacman -S pavucontrol``

- 若音箱没有播放，执行`pavucontrol`选择设备。

+ 启动``pulseaudio``服务。``pulseaudio -k``，``pulseaudio --start ``

## 参考文章

> [link](https://cloud.tencent.com/developer/article/1559535)
>
> [link](https://cloud.tencent.com/developer/article/1747847)


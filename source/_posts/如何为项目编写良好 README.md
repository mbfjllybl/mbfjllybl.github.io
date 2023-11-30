---
title: 如何为项目编写良好 README
date: 2021-11-06 13:38:33
updated:
tags: Git
categories: Git
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

# 如何为项目编写良好 README

README，它是别人对项目了解、印象的第一来源；尤其是针对开源项目，相当之重要：好比颜值之于一个人，主页之于一个公司；但很多项目并未重视这一点；各种仓库，浩如烟海，没有简洁、明晰的介绍，教人如何耐心去看？本篇文章的存在，即是为了改善这种情况。它将指导您如何写出一篇友好、易读的 README ，同时提供一键生成专业 README（模版）的工具，从而为广大开发者，解决如何书写良好 README 之烦忧；同时为诸多阅读者，缓解没有清晰 README 一窥项目主旨的苦恼。

## 1. 顶部信息

显而易见，**Logo**、**标题**、**简短描述**，对于项目是必要的；它应该位于 README 顶部，且居中显示；除此之外，还可以添加**徽章**（Badge），对项目进行标记和说明，这些好看的小图标，不仅简洁美观，而且清晰易读，您可以在这里链接更多信息，这有助于更好向他人展示自己的项目。如下。

<div align="center">
	<a href="https://bugstack.cn/md/other/guide-to-reading.html"><img src="https://bugstack.cn/images/system/CodeGuide-Read.svg"></a>
	<a href="https://bugstack.cn/images/personal/qrcode.png"><img src="https://bugstack.cn/images/system/CodeGuide-WeiXinCode.svg"></a>
	<a href="https://bugstack.cn/md/knowledge/pdf/2021-01-26-Java%E9%9D%A2%E7%BB%8F%E6%89%8B%E5%86%8CPDF%E4%B8%8B%E8%BD%BD.html"><img src="https://bugstack.cn/images/system/CodeGuide-JavaPDF.svg"></a>
	<a href="https://mp.weixin.qq.com/s/VthCUlT8oAJqKOoq5_NzSQ"><img src="https://bugstack.cn/images/system/CodeGuide-Lottery.svg"></a>
	<a href="https://github.com/fuzhengwei/CodeGuide"><img src="https://badgen.net/github/stars/fuzhengwei/CodeGuide?icon=github&color=4ab8a1"></a>
</div>

如果您习惯使用中文，但项目又是国际化的，那不妨考虑以英文来书写 README，这似乎更有逼格（毕竟代码也是英文）；但为人性化考量，也应该提供其他语言版本 README，可以在**徽章**之下，添加网页跳转链接。

## 2. 目标与哲学

为您设计的项目，写下初衷、理念和目标。对创建和维护项目背后的动机，作简短的阐述，这应该可以解释为什么该项目存在。

## 3. 先决条件

说明用户在安装和使用前，需要准备的一些先决条件，譬如：

您需要安装或升级 [Node.js](https://nodejs.org/en/)（> = `8。*`，Npm 版本 >= `5.2.0`，[Yarn](https://www.jeffjade.com/2017/12/30/135-npm-vs-yarn-detial-memo/) 作为首选）。

## 4. 安装

在特定的生态系统中，可能存在一种通用的安装方式，例如使用Yarn，NuGet或Homebrew。但是，请考虑是否有可能正在阅读README的人是新手，并且需要更多指导。

## 5. 用法

列举如何使用示例，并尽可能显示预期的输出。内联您可以演示的最小用法示例很有帮助，同时提供指向更复杂示例的 Wiki 链接（如果它们太长而无法合理地包含在自述文件中）。

## 6. 功能

您可以用列表的形式，展示项目现在已经支持的功能或特性，这有助于他人对项目存在的价值，做更深一步了解。

## 7. 屏幕截图

包括 logo / demo 截图等。

## 8. 支持

告诉人们他们可以去哪里寻求帮助。它可以是 issue 跟踪器，聊天室，电子邮件地址等的任意组合。

## 9. 测试

用代码示例描述并展示如何运行测试。

## 10. 路线图

如果您对将来的发行版有任何想法，最好在 README 文件中列出它们。

## 11. 贡献

欢迎提出请求。对于重大更改，请先打开一个 issue，以讨论您要更改的内容。请确保适当更新测试。

## 12. 作者和致谢

向那些为该项目做出贡献的人表示感谢。

## 13. 执照

[MIT](http://opensource.org/licenses/MIT)

版权所有 (c) 2020-至今，[您的名字](https://quickapp.lovejade.cn/how-to-write-a-good-readme-for-your-project/you-website-url)。

## 14. 项目状态

如果您没有足够的精力或时间来完成项目，请在 README 文件的顶部添加注释，指出开发速度已减慢或完全停止。可能有人会选择 fork 您的项目，或者，以维护者或所有者的身份自愿加入，从而使您的项目继续进行下去。您也可以明确要求维护者。


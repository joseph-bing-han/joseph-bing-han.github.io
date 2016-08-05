---
title: Eclipse3.6 中文下Jadeclipse无法打开class的修正
id: 64
categories:
  - java
  - 开发相关
date: 2011-07-12 16:14:26
tags:
---

<span style="color: #ff0000;">转载请注明来源:</span>[非凡软件工作室博客](../ "非凡软件工作室博客")

jadclipse不能运行 点击类就报 Unmatched braces in the pattern 错误
在网上找到的都是用切回英文的办法,很不爽,这样我还费劲装什么中文语言包干嘛?因噎废食啊!

终于找到了个从根本上解决的办法,居然又是该语言包,看了eclipse的babel项目bug不少啊.
修改语言包org.eclipse.jdt.core.nl_zh_3.6.0.v20101211043401.jar\org\eclipse\jdt\internal\core\util\messages_zh.properties

中的字段

disassembler_opentypedeclaration=\ \u201C{\u201D

为

disassembler_opentypedeclaration=\ '{'

打包即可﻿
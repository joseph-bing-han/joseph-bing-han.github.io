---
title: Ubuntu Flash 乱码修正
id: 28
categories:
  - Linux
date: 2011-07-10 10:41:50
tags:
---

<span style="color: #ff0000;">转载请注明来源:</span>[非凡软件工作室博客](../ "非凡软件工作室博客")

使用下面两行命令
$sudo cp /etc/fonts/conf.d/49-sansserif.conf /etc/fonts/conf.d/49-sansserif.conf.bak
$sudo rm /etc/fonts/conf.d/49-sansserif.conf
---
title: Ubuntu 10.04 sun-java-sdk
id: 26
categories:
  - java
  - Linux
  - 开发相关
date: 2011-07-10 10:40:28
tags:
---

<span style="color: #ff0000;">转载请注明来源:</span>[非凡软件工作室博客](../ "非凡软件工作室博客")

今天装red5时才注意到
ubuntu 10.04默认安装的jdk居然是open-jdk
10.04安装源里移除了sun的jdk
所以只好手工加了
$ sudo add-apt-repository "deb [http://archive.canonical.com/](http://archive.canonical.com/) lucid partner"
$ sudo apt-get update
$ sudo apt-get install sun-java6-jdk
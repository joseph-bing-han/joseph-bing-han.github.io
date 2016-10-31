---
title: 使用wget命令下载整站
id: 136
categories:
  - Linux
  - shell
date: 2014-02-19 01:51:04
tags:
---

转载请注明来源:[Blog of Joseph Han](http://blog.joseph-han.net/ "Blog of Joseph Han")

最近想做个背新概念英语单词的应用给自己用,需要从一个网站上下载mp3资源,于是决定对这个网站进行整站下载后在整理数据。

<span style="color: #ff0000;"><span style="color: #000000;">用linux下常用命令wget进行整站下载（递归下载至本地） </span>
<span style="color: #000000;">这个命令可以以递归的方式下载整站，并可以将下载的页面中的链接转换为本地链接。 </span></span>

<span style="color: #000000;">wget加上参数之后，即可成为相当强大的下载工具。 </span>

<span style="color: #000000;">wget -r -p -np -k http://xxx.com/abc/ </span>

<span style="color: #000000;">-r,  --recursive（递归）          specify recursive download.（指定递归下载） </span>
<span style="color: #000000;">-k,  --convert-links（转换链接）      make links in downloaded HTML point to local files.（将下载的HTML页面中的链接转换为相对链接即本地链接） </span>
<span style="color: #000000;">-p,  --page-requisites（页面必需元素）    get all images, etc. needed to display HTML page.（下载所有的图片等页面显示所需的内容） </span>
<span style="color: #000000;">-np, --no-parent（不追溯至父级）          don't ascend to the parent directory. </span>

<span style="color: #000000;">另外断点续传用-nc参数 日志 用-o参数</span>
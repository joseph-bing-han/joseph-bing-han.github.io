---
title: Mac下为php安装ibm_db2扩展
id: 147
categories:
  - DB2
  - OSX
  - 开发相关
date: 2014-03-31 14:15:16
tags:
---

转载请注明来源:[Blog of Joseph Han](http://blog.joseph-han.net/ "Blog of Joseph Han")

<span style="color: #000000;">在Mac下用pecl install ibm_db2为php安装db2扩展一直不成功.
今日一同事提点下终于安装上了.</span>

<span style="color: #000000;">首先要修改/usr/local/bin/autom4te
</span>第94行处
#fatal "need GNU m4 1.4 or later: $m4"
#if system "$m4 --help &lt;/dev/null 2&gt;&amp;1 | grep reload-state &gt;/dev/null";
注释掉94,95行

然后再进行下载编译安装
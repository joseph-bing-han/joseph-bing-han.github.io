---
title: SoapUI 连接 DB2
id: 174
tag:
  - DB2
  - Linux
  - 开发相关
date: 2015-05-07 22:19:05

---

转载请注明来源:[Blog of Joseph Han](http://blog.joseph-han.net/ "Blog of Joseph Han")

今天用SoapUI连接DB2,无论如何设置都不成功,Google的结果也没什么帮助.
无意中发现,要使用JDBC连接DB2,需要先添加DB2驱动的jar包.
要将驱动jar包放在bin/ext目录下

[![](http://ffblog-wordpress.stor.sinaapp.com/uploads/2015/05/soapui-db2.gif "soapui-db2")](http://ffblog-wordpress.stor.sinaapp.com/uploads/2015/05/soapui-db2.gif)
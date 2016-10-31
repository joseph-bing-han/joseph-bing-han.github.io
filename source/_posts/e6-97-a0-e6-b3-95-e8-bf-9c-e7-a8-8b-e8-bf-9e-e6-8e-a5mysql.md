---
title: 无法远程连接MySQl
id: 11
categories:
  - Linux
  - mysql
  - 开发相关
date: 2011-07-10 10:27:25
tags:
---

<span style="color: #ff0000;">转载请注明来源:</span>[Blog of Joseph Han](../ "Blog of Joseph Han")

今天是重装系统后第一次远程连接MySQL,报错
ERROR 2003 (HY000): Can't connect to MySQL server on '10.4.2.183' (111)
发现$ netstat -an | more
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN

MySQL绑定在127.0.0.1:3306上
修改/etc/mysql/my.cnf
bind-address        = 127.0.0.1     =====&gt; bind-address        = 0.0.0.0

$ sudo /etc/init.d/mysql restart

终于正常了

&nbsp;
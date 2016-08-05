---
title: ubuntu iptables规则保存与自动加载
id: 133
categories:
  - Linux
date: 2014-02-06 01:10:57
tags:
---

转载请注明来源:[<span style="color: #ff0000;">非凡软件工作室博客</span>](http://blog.feifansoft.tk/ "非凡软件工作室博客")

ubuntu下想保存iptables规则,可以iptables-persistent
sudo apt-get install iptables-persistent

iptables-persistent其实是一个脚本,具体在/etc/init.d/iptables-persistent.

想保存当前规则时,使用 sudo service iptables-persistent save.
重新加载已保存的规则使用 sudo service iptables-persistent reload
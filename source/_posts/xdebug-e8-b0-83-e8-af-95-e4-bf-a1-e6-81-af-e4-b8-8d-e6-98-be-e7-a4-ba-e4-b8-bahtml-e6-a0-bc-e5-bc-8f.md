---
title: Xdebug调试信息不显示为html格式
id: 9
categories:
  - Linux
  - php
  - 开发相关
date: 2011-07-10 10:23:22
tags:
---

<span style="color: #ff0000;">转载请注明来源:</span>[Blog of Joseph Han](http://ffblog.sinaapp.com "Blog of Joseph Han")

重装系统后,php页面里内的调试信息不按html格式显示
开始以为是忘记装php5-xdebug.so的问题,装了xdebug还是不行
最后发现,Ubuntu Server安装php是按照生产模式安装的,php的配置项<span style="color: #0000ff;">html_errors</span>=<span style="color: #ff0000;">Off</span>;
改为<span style="color: #0000ff;">html_errors</span>=<span style="color: #ff0000;">On</span>;后正常显示html格式的调试信息了
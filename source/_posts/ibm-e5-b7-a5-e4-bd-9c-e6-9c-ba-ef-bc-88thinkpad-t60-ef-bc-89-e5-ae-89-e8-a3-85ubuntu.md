---
title: IBM工作机（ThinkPad T60）安装ubuntu
id: 74
tag:
  - Linux
date: 2012-03-13 15:30:48

---

<span style="color: #ff0000;">转载请注明来源:</span>[Blog of Joseph Han](../ "Blog of Joseph Han")

最近换了工作，现在在IBM。

发的工作机比较老，是ThinkPad T60。默认装的windows，启动、使用都很慢，最近Notes又起不来了。

决定自己给换成ubuntu desktop 10.04。因为windows系统还的保留，所有是wibu方式安装的。

安装后要安装IBM专用的软件包
[http://ocdc.hursley.ibm.com/ocdc/html/ocdc-installer](http://ocdc.hursley.ibm.com/ocdc/html/ocdc-installer "http://ocdc.hursley.ibm.com/ocdc/html/ocdc-installer")
(注：此地址为IBM内网)

安装过程中会出错，退出后执行

sudo dpkg --configure -a

等待完成即可

PS：
今天给T410安装时失败，提示密钥错误。
多方查找后发现：/usr/share/pyshared/mpw/installer/installLib.py中，下载密钥deb包时的wget命令使用了-T 3设置了超时，在网络不畅(景德镇的网络就没畅通过）时超时造成下载失败无法进行后续安装，将-T 3全部去除即可
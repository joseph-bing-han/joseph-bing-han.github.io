---
title: 神州T1600笔记本 安装Ubuntu10.04Desktop
id: 35
tag:
  - Linux
date: 2011-07-10 10:45:36

---

<span style="color: #ff0000;">转载请注明来源:</span>[Blog of Joseph Han](../ "Blog of Joseph Han")

我的笔记本是神州T1600的，集成的SIS的显示芯片，在linux下兼容性不是很好，经常会出各种问题。最新版的ubuntu10.04已经出了，预计对这倒霉本子支持会好一点，安装过程如下：
1.在Windows7下进行wubi模式安装，修改**Safe graphic mode**<span style="font-family: 宋体;">的参数，追加</span><span style="color: #ff0000; font-family: 宋体;"> vga=788 <span style="color: #000000;">重启后</span></span>选**Safe graphic mode**安装

2.安装后登录系统，修改源
$sudo gedit /etc/apt/sources.list
<span style="color: #274e13;">##############################
#以下是我用的源</span>
deb http://mirrors.163.com/ubuntu/ lucid main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ lucid-security main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ lucid-updates main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ lucid-proposed main restricted universe multiverse
deb http://mirrors.163.com/ubuntu/ lucid-backports main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ lucid main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ lucid-security main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ lucid-updates main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ lucid-proposed main restricted universe multiverse
deb-src http://mirrors.163.com/ubuntu/ lucid-backports main restricted universe multiverse

deb http://mirrors.sohu.com/ubuntu/ lucid main restricted universe multiverse
deb http://mirrors.sohu.com/ubuntu/ lucid-security main restricted universe multiverse
deb http://mirrors.sohu.com/ubuntu/ lucid-updates main restricted universe multiverse
deb http://mirrors.sohu.com/ubuntu/ lucid-proposed main restricted universe multiverse
deb http://mirrors.sohu.com/ubuntu/ lucid-backports main restricted universe multiverse
deb-src http://mirrors.sohu.com/ubuntu/ lucid main restricted universe multiverse
deb-src http://mirrors.sohu.com/ubuntu/ lucid-security main restricted universe multiverse
deb-src http://mirrors.sohu.com/ubuntu/ lucid-updates main restricted universe multiverse
deb-src http://mirrors.sohu.com/ubuntu/ lucid-proposed main restricted universe multiverse
deb-src http://mirrors.sohu.com/ubuntu/ lucid-backports main restricted universe multiverse

deb http://tw.archive.ubuntu.com/ubuntu lucid main restricted universe multiverse
deb http://tw.archive.ubuntu.com/ubuntu lucid-security main restricted universe multiverse
deb http://tw.archive.ubuntu.com/ubuntu lucid-updates main restricted universe multiverse
deb http://tw.archive.ubuntu.com/ubuntu lucid-backports main restricted universe multiverse
deb http://tw.archive.ubuntu.com/ubuntu lucid-proposed main restricted universe multiverse
deb-src http://tw.archive.ubuntu.com/ubuntu lucid main restricted universe multiverse
deb-src http://tw.archive.ubuntu.com/ubuntu lucid-security main restricted universe multiverse
deb-src http://tw.archive.ubuntu.com/ubuntu lucid-updates main restricted universe multiverse
deb-src http://tw.archive.ubuntu.com/ubuntu lucid-backports main restricted universe multiverse
deb-src http://tw.archive.ubuntu.com/ubuntu lucid-proposed main restricted universe multiverse

deb http://archive.ubuntu.com/ubuntu/ lucid main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu/ lucid-security main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu/ lucid-updates main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu/ lucid-proposed main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu/ lucid-backports main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu/ lucid main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu/ lucid-security main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu/ lucid-updates main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu/ lucid-proposed main restricted universe multiverse
deb-src http://archive.ubuntu.com/ubuntu/ lucid-backports main restricted universe multiverse
<span style="color: #274e13;">##############################</span>
$sudo apt-get update

3.显示相关设置
1)安装sis显卡驱动
$cd /tmp
$wget http://blogimg.chinaunix.net/blog/upfile2/100504152323.zip
$unzip xorg-driver-sis671-0.9.1-fixed-build.zip
$sudo cp sis671_drv.* /usr/lib/xorg/modules/drivers/

2)处理启动时控制台的花屏
$echo blacklist vga16fb | sudo tee /etc/modprobe.d/blacklist-vga16fb.conf
$sudo update-initramfs -u

3)修改/etc/X11/xorg.conf配置
$sudo gedit /etc/X11/xorg.conf
<span style="color: #274e13;">###########################
#文件内容</span>
Section "Device"
Identifier   "Configured Video Device"
Driver      "sis671"
<span style="color: #274e13;">#   BusID      "PCI:1:0:0" </span>
Option      "EnableSiSCtrl"      "yes"
<span style="color: #274e13;">#   Option          "XvDefaultAdaptor"   "Blitter" </span>
EndSection

Section "Monitor"
Identifier   "Configured Monitor"
Option      "DPMS"         "yes"
EndSection

Section "Screen"
Identifier   "Default Screen"
Monitor      "Configured Monitor"
Device      "Configured Video Device"
DefaultDepth   16
EndSection
<span style="color: #274e13;">###########################</span>

4.添加root帐号的登录及sudo的密码
$sudo passwd root
设置root密码
$sudo gedit /etc/gdm/gdm.schemas
72行，&lt;default&gt;<span style="color: #ff0000;">root</span>&lt;/default&gt;增加<span style="color: #ff0000;">root</span>
78 行，&lt;default&gt;bin,root,daemon,adm,lp,sync,shutdown,halt,mail,news,uucp,operator,nobody,nobody4,noaccess,postgres,pvm,rpm,nfsnobody,pcap&lt;/default&gt;
去除<span style="color: #ff0000;">root</span>
$sudo gedit /etc/sudoers
最后一行，改为
<span style="color: #ff0000;">%admin ALL=(ALL) NOPASSWD: NOPASSWD: ALL </span>
这样每次sudo就不需要再输入密码了

完成以上设置后系统基本就没有什么问题了。
Ubuntu10.04对硬件的支持很好，连神州T1600这样杯具的本本的硬件都能完全支持（除了更杯具的sis显卡芯片），Fn快捷建控制的声音、屏 幕亮度、触屏开关、无线网卡开关全部都好用，特别是无线网卡，Realtek RTL8187 Wireless 802.11g 54Mbps USB  2.0 Network Adapter的卡以前都得手工加驱动，这次直接都能用了。
虽然在Linux社区Ubuntu的名声一直都不好，但我仍然要感谢它及其开发维护人员。
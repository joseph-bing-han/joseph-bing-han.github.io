---
title: 硬盘安装 Ubuntu Server 10.04
id: 33
categories:
  - Linux
date: 2011-07-10 10:44:16
tags:
---

<span style="color: #ff0000;">转载请注明来源:</span>[非凡软件工作室博客](../ "非凡软件工作室博客")

工作用的台机的Ubuntu Desktop从9.10升级到10.04后出了各种各样的问题，为了彻底解决，决定重新安装。这次直接装Server版，然后手工装ubuntu-desktop。安装过程如下：
**
备份个人数据**
将/home下数据打包
**
开始安装
**切换到WindowsXP**
**到
http://archive.ubuntu.com/ubuntu/dists/lucid/main/installer-i386/current/images/hd-media/
下载下面3各文件
boot.img.gz
initrd.gz
vmlinuz
将这些文件和安装镜像*.iso拷贝到C盘
原有系统中已经安装了grub2,所以不需要grub4dos
重启系统进入grub界面,按"C"键进入命令行
输入以下：
set root=(hd0,1)
linux (hd0,1)/vmlinuz root=/dev/ram ramdisk_size=32000 devfs=mount,dall
initrd (hd0,1)/initrd.gz
boot

重启系统，进入安装界面，一步一步选择配置安装,
选择 base server,lmap server ,mail server , ssh server
完成后重启

设置root密码
passwd root

远程ssh连接
打开/etc/apt/sources.list,增加国内的源
到http://wiki.ubuntu.org.cn/Qref/Source选合适的源列表

我用的源列表
##########################################################################################################
#163源
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

#shou源
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

#欧洲官方源
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

#台湾官方源
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
##########################################################################################################

sudo apt-get update

安装桌面系统,以前一直用gnome,这次决定试用一下kde
sudo apt-get install kubuntu-desktop
#也可以安装gonme，用$sudo apt-get install uuntu-desktop
我这平均每秒900k,大约12分钟下完,25分钟安装完成

安装中文环境
sudo apt-get install language-pack-kde-zh-hans openoffice.org-l10n-zh-cn

编译环境
sudo apt-get install build-essential autoconf automake

重启,登录桌面
sudo reboot

开启xdmcp,允许root登录
sudo kate /etc/kde4/kdm/kdmrc

找到[Xdmcp]，编辑Enable=true。
AllowRootLogins=true

sudo kate /etc/kde4/kdm/Xaccess
添加一行*

另外要把当前用户加到audio组,否则没有声音

去除sudo的密码输入
sudo kate /etc/sudoers
%admin ALL=(ALL)
改為
%admin ALL=(ALL) NOPASSWD: NOPASSWD: ALL

#################################################################
全自动安装微软雅黑字体- Ubuntu

支持在Ubuntu8.10、Ubuntu9.04系统上自动安装微软雅黑、monaco-linux、lucida-console等字体。ubuntu
而且可以设置LED、CRT两种不同的显示器的渲染效果，无需手工配置，实现自动化安装。
使用方法如下：
打开命令控制器，然后运行下面的脚本（可以通过粘贴复制实现）：

wget -O get-fonts.sh.zip http://files.cnblogs.com/DengYangjun/get-fonts.sh.zip
unzip -o get-fonts.sh.zip 1&gt;/dev/null
chmod a+x get-fonts.sh
./get-fonts.sh

删除下载的字体安装脚本文件：

rm get-fonts.sh get-fonts.sh.zip 2&gt;/dev/null

恢复以前的字体设置：

cd /etc/fonts/conf.avail
sudo mv 51-local.conf.old 51-local.conf 2&gt;/dev/null
sudo mv 69-language-selector-zh-cn.conf.old 69-language-selector-zh-cn.conf 2&gt;/dev/null
sudo rm -f -r /usr/share/fonts/truetype/myfonts 2&gt;/dev/null
cd -
##########################################################

安装新立德，如果安的是gonme就不用了
sudo apt-get install synaptic

安装雷鸟,pidgin,skype,linuxQQ,alltray
到http://ubuntu-tweak.com/安装ubuntu-tweak
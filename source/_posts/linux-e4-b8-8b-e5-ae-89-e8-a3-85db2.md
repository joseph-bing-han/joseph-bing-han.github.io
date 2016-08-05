---
title: Linux下安装DB2
id: 117
categories:
  - DB2
  - Linux
date: 2012-12-18 22:34:31
tags:
---

参考文献：

[How-to: Ubuntu 7.10 Server x86 32-bit and DB2 Express-C v9.5](http://www.ibm.com/developerworks/forums/thread.jspa?threadID=187514)

[DB2 v9.7 Infomation Center](http://publib.boulder.ibm.com/infocenter/db2luw/v9r7/index.jsp?topic=/com.ibm.db2.luw.qb.dbconn.doc/doc/c0004770.html)

&nbsp;

场景：在IBM System x3550服务器上，Ubuntu Server 8.0.4操作系统中以db2_install 命令安装DB2 v9.7 ESE trial数据库。

&nbsp;

一.安装前的步骤

1.安装几个包，执行命令：

sudo apt-get install libaio1
sudo apt-get install libstdc++5

&nbsp;

2.copy安装文件到目标系统，并解压缩。如果是从windwos解压缩完再传到Ubuntu上，那么需要设置安装文件及相关的文件以可执行属性。

&nbsp;

3.设置ulimit（Server版这步就略过了）

-------------------------------------

﻿修改 Ubuntu ulimit 限制
<div>

最近，网站一到高峰期，CPU就会飙升到100%，但内存，IO，网络等一切正常，Google了一下，有可能是ulimit的问题，马上查看文件句柄数限制
` ulimit -n `
得到的结果是：1024，这个值对生产中的服务显得偏小。
网上给出的解决方案，大部分是直接输入
` ulimit -SHn 51200 # 51200可自己根据应用调整 `
此法缺点很明显，一旦退出登陆，设置就失效了。
也有说直接把该命令写到/etc/rc.d/rc.local中的，今天找到一个正确的做法
1.打开/etc/security/limits.conf，里面有很详细的注释，找到如下设置(如果没有就插入)
` * soft nofile 51200
* hard nofile 51200 `
2.编辑/etc/pam.d/common-session，加入一行
` session required pam_limits.so `
3.编辑/etc/profile，加入
` ulimit -SHn 51200 `
重启服务器，再次登陆，查看句柄数，已经正确设置为51200。
如果有碰到类似情况，可以检查一下看看是不是文件句柄数设置太小引起的。

</div>
-------------------------------------

&nbsp;

4.修改内核参数

1)查看内核参数

ipcs -l

显示如下：
<div>cuser@localhost:~$ ipcs -l
------ Shared Memory Limits --------
max number of segments = 4096
max seg size (kbytes) = 32768
max total shared memory (kbytes) = 8388608
min seg size (bytes) = 1
------ Semaphore Limits --------
max number of arrays = 128
max semaphores per array = 250
max semaphores system wide = 32000
max ops per semop call = 32
semaphore max value = 32767
------ Messages: Limits --------
max queues system wide = 16
max size of message (bytes) = 8192
default max size of queue (bytes) = 16384</div>
&nbsp;

2)编辑/etc/sysctl.conf文件。如果此文件不存在，那么应创建它。加入如下内容：
<div>kernel.sem=250 256000 32 1024
#示例：用于 64 位系统的 shmmax
kernel.shmmax=1073741824
#示例：用于4GB内存的 90% 的 shmall
kernel.shmall=943718
kernel.msgmax=65535
kernel.msgmnb=65535</div>
对于这些参数的说明，参考DB2 infocenter：

建议将 SHMMAX 值设置为与系统上物理内存量相等。然而，x86 系统上需要的最小值为 268435456（256 MB），而在 64 位系统上，该值将为 1073741824（1 GB）。
缺省情况下，SHMALL 设置为 8 GB（8388608 KB = 8 GB）。如果您具有的物理内存超过此值，并且要将该物理内存用于  DB2，那么此参数增大到大约是计算机的物理内存的 90%。例如，假定计算机系统上有 16 GB 内存，并将这些内存主要用于 DB2，那么  SHMALL 应该设置为 3774873（16 GB 的 90% 为 14.4 GB；将 14.4 GB 除以 4  KB（这是基页大小）得到的结果为 3774873）。IPCS 输出已将 SHMALL  转换为千字节（KB）。这是因为内核需要将此值作为页数。如果要升级到 DB2 版本 9.7 并且没有使用缺省 SHMALL 设置，那么必须将  SHMALL 设置另外增大 4 GB。快速通信管理器（FCM）要求这样增大内存，以供另外的缓冲区或信道使用。
内核参数 sem 由以下四个标记组成：SEMMSL、SEMMNS、SEMOPM 和 SEMMNI。SEMMNS 是 SEMMSL 乘以  SEMMNI 的结果。数据库管理器要求根据需要增大数组的数目（SEMMNI）。通常，SEMMNI  应两倍于系统上预期的最大代理程序数乘以数据库服务器计算机上的逻辑分区数再加上数据库服务器计算机上的本地应用程序连接数。
MSGMNI 将影响可以启动的代理进程数，MSGMAX 将影响一个队列中可以发送的消息大小，而 MSGMNB 将影响队列大小。
应将 MSGMAX 更改为 64 KB（即，65535 个字节），MSGMNB 应增大为 65535。

&nbsp;

3)运行sysctl 时附带-p参数，以从缺省文件/etc/sysctl.conf中装入sysctl设置：

sudo sysctl -p

&nbsp;

二.安装数据库

1.运行安装命令

sudo ./db2_install

2.指定安装路径，一般不更改路径，中文环境键入“是”加回车。

4.一路安装，末了提示一个警告，说有小错误，不去管它，算是安装完。

&nbsp;

三.安装后步骤

1.创建用户和用户组

1)创建用户组：

sudo groupadd -g 999 db2iadm1
sudo groupadd -g 998 db2fadm1
sudo groupadd -g 997 dasadm1
2)创建用户：
sudo useradd -u 1004 -g db2iadm1 -m -d /home/db2inst1 db2inst1
sudo useradd -u 1003 -g db2fadm1 -m -d /home/db2fenc1 db2fenc1
sudo useradd -u 1002 -g dasadm1 -m -d /home/dasusr1 dasusr1
3)设置用户的密码
sudo passwd db2inst1
sudo passwd db2fenc1
sudo passwd dasusr1

&nbsp;

2.创建DB2管理服务
sudo /opt/ibm/db2/V9.7/instance/dascrt -u dasusr1

3.创建DB2实例
sudo /opt/ibm/db2/V9.7/instance/db2icrt -a server -u db2fenc1 db2inst1

4.设置DB2使用的端口
su - db2inst1
/bin/bash
chsh -s /bin/bash db2inst1（DB2需要bash，这个命令修改默认的sh）

/home/db2inst1/sqllib/db2profile

db2 update dbm cfg using svcename 50000

&nbsp;

5.设置DB2通讯协议
db2set DB2COMM=tcpip
6.启动DB2
db2start
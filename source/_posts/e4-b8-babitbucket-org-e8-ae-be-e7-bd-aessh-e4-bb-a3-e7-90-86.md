---
title: 为bitbucket.org设置ssh代理
id: 125
tag:
  - git
  - Linux
  - shell
  - 开发相关
date: 2013-06-11 11:42:32

---

转载请注明来源:[Blog of Joseph Han](http://blog.joseph-han.net/ "Blog of Joseph Han")

最近[https://bitbucket.org](https://bitbucket.org)被墙，推送代码受到了影响。

最初使用VPN来翻墙，但在公司内网时无法连接VPN，不得不想其他办法，
最终采用了ssh代理的方法：

在~/.ssh/config (没有这个文件就创建一个)，增加以下内容：
<pre>Host bitbucket.org
  User bitbucket_user_name
  Port 22
  Hostname bitbucket.org
  TCPKeepAlive yes
  IdentitiesOnly yes
  ProxyCommand ssh ssh_user_name@ssh_host nc %h %p 2&gt; /dev/null</pre>
再使用git命令时，就会使用ssh代理来连接bitbucket.org服务器，
但有点麻烦的是需要两次登陆，得先输入一次ssh代理服务器的登陆密码。
要去掉这次验证，只需要将~/.ssh/id_rsa.pub 上传到ssh代理服务器的~/.ssh目录下
并改名为authorized_keys即可

使用ssh代理后git命令的反应会稍稍慢一些，毕竟需要两次登陆验证，可以理解。
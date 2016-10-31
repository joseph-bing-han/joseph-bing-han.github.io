---
title: eclipse3.6 加中文包后自动提示/补全信息显示错误
id: 14
categories:
  - java
  - 开发相关
  - 未分类
date: 2011-07-10 10:29:30
tags:
---

<span style="color: #ff0000;">转载请注明来源:</span>[Blog of Joseph Han](../ "Blog of Joseph Han")

昨天刚刚安装了eclipse3.6+Babel Language Packs-0.8.0 简体中文包.
总体感觉不错,甚至比3.5还流畅(不知道是不是错觉).
全都都设置好后开始开发,
使用中才发现,自动提示/自动补全时的提示框,每行内容里都多了一堆的东西,把Tip的长度都拉长了
看起来很不方便,就像下面这个样子
[![](http://ffblog-wordpress.stor.sinaapp.com/uploads/2011/07/eclipse.jpg "eclipse")](http://ffblog-wordpress.stor.sinaapp.com/uploads/2011/07/eclipse.jpg)
看起来非常别扭.
于是开始google原因,用"<span style="color: #ff0000;">JavaElementLabels_comma_string</span>"做关键字进行检索,内容不是很多
最终把目标锁定在了eclipse源码中,发现是一个文言key
恍然大悟--原来是中文包的问题
以英文为默认语言重启eclipse,果然一切正常.

先自己修正,然后再说别的
1.解包org.eclipse.jdt.ui.nl_zh_3.6.0.v20100814043401.jar
2.将文件org.eclipse.jdt.internal.ui.JavaUIMessages_zh.properties第103~109行全部删除
3.重新打包

重启eclipse,
终于正常了

我从eclipse2.1开始就用的中文版,从来没遇到过语言包的bug
这次是涨见识了![](http://img.baidu.com/hi/face/i_f07.gif)

准备在eclipse的Bugzilla上申请个账号,给Babel Language Packs项目提bug去![](http://img.baidu.com/hi/face/i_f47.gif)
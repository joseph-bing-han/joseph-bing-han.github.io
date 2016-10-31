---
title: "Windows7 Visual Studio 2010 字体模糊问题的解决\t "
id: 40
tag:
  - C++
  - 开发相关
date: 2011-07-10 10:52:21

---

<span style="color: #ff0000;">转载请注明来源:</span>[Blog of Joseph Han](../ "Blog of Joseph Han")

最近为了编译pandion最新版,安装了Visual Studio 2010,系统是windows7,安装后发现非常悲剧.

[![](http://ffblog-wordpress.stor.sinaapp.com/uploads/2011/07/vs2010.jpg "vs2010")](http://ffblog-wordpress.stor.sinaapp.com/uploads/2011/07/vs2010.jpg)

字体模糊的让我有直接拆卸掉VS2010的冲动.

查了半天,都说是WPF的问题,要开启ClearType.可我用的是Windows7,默认已经开启了.

也有说环境字体换成支持ClearType的微软雅黑,我也换了,涛声依旧啊 : (

今天无意中关闭了ClearType后发现,VS2010终于显示正常了,惊喜啊!!!

可是Window7在关闭了ClearType后又惨不忍睹了,于是,努力寻找VS自身关闭ClearType的功能.

最后,终于找到了
"工具"-&gt;"选项"-&gt;"环境"-&gt;"常规"-&gt;"视觉体验"-&gt;"如果可以,请使用硬件图形加速"

Visual Studio 2010关掉"如果可以,请使用硬件图形加速"就不会再出现字体模糊的问题了
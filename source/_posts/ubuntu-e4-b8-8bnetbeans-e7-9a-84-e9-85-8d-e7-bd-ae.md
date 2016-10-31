---
title: ubuntu 下netbeans的配置
id: 78
tag:
  - Linux
date: 2012-03-13 15:41:18

---

<span style="color: #ff0000;">转载请注明来源:</span>[Blog of Joseph Han](../ "Blog of Joseph Han")

现在使用的ThinkPad T60上的ubuntu中有两个JVM,一个是Oracle的1.7另一个是IBM自己的1.6，默认是IBM的
但用默认的JVM运行netbeans时，界面不是仿GTK的而是JVM的，没有时间研究怎么设置JVM了，直接修改配置文件的默认JDK路径

我使用是netbeans7.1.1的zip版

配置文件在解压路径下的etc/netbeans.conf

增加以下内容

netbeans_jdkhome="/usr/lib/jvm/java-7-oracle"

同时在

netbeans_default_options项目中追加“-J-Dawt.useSystemAAFontSettings=lcd”来开启编辑器页面的文字反锯齿效果

&nbsp;

&nbsp;
---
title: Ubuntu Flash 乱码修正
id: 28
tag:
  - Linux
date: 2011-07-10 10:41:50

---

<span style="color: #ff0000;">转载请注明来源:</span>[Blog of Joseph Han](../ "Blog of Joseph Han")

使用下面两行命令
$sudo cp /etc/fonts/conf.d/49-sansserif.conf /etc/fonts/conf.d/49-sansserif.conf.bak
$sudo rm /etc/fonts/conf.d/49-sansserif.conf
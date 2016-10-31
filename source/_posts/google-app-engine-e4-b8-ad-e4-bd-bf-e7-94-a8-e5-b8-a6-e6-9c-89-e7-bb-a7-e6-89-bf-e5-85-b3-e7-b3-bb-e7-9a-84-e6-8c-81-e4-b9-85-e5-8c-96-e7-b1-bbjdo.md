---
title: Google App Engine中使用带有继承关系的持久化类(JDO)
id: 21
categories:
  - java
  - 开发相关
date: 2011-07-10 10:37:37
tags:
---

<span style="color: #ff0000;">转载请注明来源:</span>[Blog of Joseph Han](../ "Blog of Joseph Han")

最近在写个实用的Google App Engine应用--家庭记账系统,用于以家庭为单位,记录收支账目.
开发中,想实现一个带有继承关系的持久化类(JDO方式),以便在父类中实现例如id,createDate,createBy,deleted之类的,
所有子类都包括的属性,简化子类编码.
开始的代码如下:

public class BasePo {
@Persistent
protected Date createDate = new Date();
@Persistent
protected Long createBy;
}

@PersistenceCapable(identityType = IdentityType.APPLICATION)
public class User extends BasePo {
@PrimaryKey
@Persistent(valueStrategy = IdGeneratorStrategy.IDENTITY)
protected Long id;
}

开始用的时候,感觉保存,查找都还算正常.

结果今天仔细验证数据内容才发现,子类实例的createDate,createBy等从BasePo继承的自段根本就没有被持久化![](http://img.baidu.com/hi/face/i_f31.gif)

google了一大气,也没有找到这方面的中文资料,还好最后找到了英文的.
DataNucleus的官方说明:[http://www.datanucleus.org/products/accessplatform_1_1/jdo/orm/inheritance.html](http://www.datanucleus.org/products/accessplatform_1_1/jdo/orm/inheritance.html)

"<span style="color: #ff0000;">Superclass table</span>"的会有一个父类表,而且数据量是随子类数据量而增大的,会影响效率.
"<span style="color: #ff0000;">Complete table</span>"每个子类都会有一张表,就选这个方法了![](http://img.baidu.com/hi/face/i_f01.gif)

修改后的代码如下:

@PersistenceCapable(identityType = IdentityType.APPLICATION)
@Inheritance(customStrategy = "complete-table")
public class BasePo {

@PrimaryKey
@Persistent(valueStrategy = IdGeneratorStrategy.IDENTITY)
protected Long id;

@Persistent
protected Date createDate = new Date();

@Persistent
protected Long createBy;
}
@PersistenceCapable(identityType = IdentityType.APPLICATION)
public class User extends BasePo {
}

子类直接继承BasePo连主键都不需要声明了.

实际测试,增删改查都没有问题.

ps:以前就一直想在GAE里做成这种带继承的JDO持久化类,一直没有找到资料,
网上中文的GAE资料就很少(大部分还是p版而不是j版的),
介绍如何用JDO的就几乎没有了(可能是因为太基础了,大家都省略不提了,可苦了我这种从C转到java的人了![](http://img.baidu.com/hi/face/i_f03.gif))
所以特意把自己的经历写出来,希望能帮助到类似我这样的人.
本人E文很烂,如果对上面的资料理解的有误,希望大家能给指正.
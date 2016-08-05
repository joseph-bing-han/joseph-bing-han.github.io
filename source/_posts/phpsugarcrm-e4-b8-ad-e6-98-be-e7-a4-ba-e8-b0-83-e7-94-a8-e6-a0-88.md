---
title: PHP(SugarCRM)中显示调用栈
id: 99
categories:
  - php
  - 开发相关
date: 2012-11-19 21:12:17
tags:
---

转载请注明来源:[非凡软件工作室博客](http://blog.feifansoft.tk/ "非凡软件工作室博客")

最近调试SugarCRM时，总觉得SQL日志内容过于简单，无法查看该条SQL的调用栈，不利于分析SQL的层级关系。于是google了一下PHP输出调用栈的函数，找到结果如下：

PHP5内置函数：debug_print_backtrace()和debug_backtrace()
这两个函数实现相同的目的——记录php函数的调用堆栈——但是表现不同。debug_print_backtrace直接在输出内容中打印出调用堆栈内容，debug_backtrace将调用堆栈以数组的形式返回供php程序使用。

例子：
$e = new Exception();
$this-&gt;log-&gt;info("\n------Query Start------\nQuery Trace:\n" . $e-&gt;getTraceAsString());
$this-&gt;log-&gt;info("Query:\n" . $sql . "\n------Query End------");

输出结果：
------Query Start------
Query Trace:
#0 /home/joseph/code/ult/sugarcrm/include/database/IBMDB2Manager.php(306): IBMDB2Manager-&gt;query('SELECT * FROM (...', true, 'Retrieving reco...')
#1 /home/joseph/code/ult/sugarcrm/data/SugarBean.php(5088): IBMDB2Manager-&gt;limitQuery('SELECT teams.* ...', 0, 1, true, 'Retrieving reco...')
#2 /home/joseph/code/ult/sugarcrm/modules/Users/User.php(1534): SugarBean-&gt;retrieve_by_string_fields(Array)
#3 /home/joseph/code/ult/sugarcrm/modules/Users/User.php(1528): User::staticGetPrivateTeamID('1')
#4 /home/joseph/code/ult/sugarcrm/include/MVC/View/SugarView.php(234): User-&gt;getPrivateTeamID()
#5 /home/joseph/code/ult/sugarcrm/include/MVC/View/SugarView.php(112): SugarView-&gt;_trackView()
#6 /home/joseph/code/ult/sugarcrm/include/MVC/Controller/SugarController.php(338): SugarView-&gt;process()
#7 /home/joseph/code/ult/sugarcrm/include/MVC/Controller/SugarController.php(285): SugarController-&gt;processView()
#8 /home/joseph/code/ult/sugarcrm/include/MVC/SugarApplication.php(82): SugarController-&gt;execute()
#9 /home/joseph/code/ult/sugarcrm/index.php(36): SugarApplication-&gt;execute()
#10 {main}
Mon Nov 19 21:08:27 2012 [20118][1][INFO] Query:
SELECT * FROM (SELECT teams.* FROM teams  WHERE associated_user_id = '1' AND deleted=0) LIMIT  1 OPTIMIZE FOR 1 ROWS
------Query End------

&nbsp;

&nbsp;
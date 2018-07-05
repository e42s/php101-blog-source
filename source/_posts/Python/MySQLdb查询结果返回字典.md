---
title: MySQLdb查询结果返回字典
categories: F-Python
permalink: mysqldb-query-results-returned-to-the-dictionary
translate_title: mysqldb-query-results-return-to-the-dictionary
date: 2016-04-25 17:06:49
tags:
description:
---
## 概述
在使用Python操作MySQL的时候，大家都习惯使用`MySQLdb`,在使用该模块操作数据库的时候，查询返回的结果默认是元素(tuple)而不是字典(dict)，这样在获取结果的时候，就要根据数字索引来取数据，不如使用字典方便。  

在网上查找了下，找到一种返回结果可以是字典的方式。


## 实现
具体的方法就是在获取`cursor`的时候，传一个获取字典的参数:

```
cursor = db.cursor(MySQLdb.cursors.DictCursor)
```

完整的示例代码如下:

```
try:
    db = MySQLdb.connect(host=host, user=user, passwd=passwd, db=database, port=port, charset=charset)
except Exception, e:
    pp(str(e))

cursor = db.cursor(MySQLdb.cursors.DictCursor)
raw_sql = "select * from wty_task"
cursor.execute(raw_sql)
rows = cursor.fetchall()

```

## 总结
感觉最近博客的更新进度还是跟不上来，一是时间没有规划好，二是容易犯懒，(⊙o⊙)…改掉这些毛病吧

<br />
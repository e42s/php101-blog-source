---
title: MySQL存储emoji表情
categories: B-MySQL
permalink: mysql-store-emoji-expresion
translate_title: mysql-stores-emoji-expression
date: 2015-12-30 14:36:58
tags:
description:
---
## 概述
做了一套微信公众平台相关的程序，最近发现一个问题，在通过微信接口获取用户信息(微信昵称、性别等)的时候发现，用户信息并没有存入数据库，查了一下log，发现SQL语句执行错误。  

查了下资料，发现了问题所在。


## 问题
错误的SQL语句形式如下:
```
UPDATE `tdy_wx_user` SET `subscribe`='1',`openid`='oYUwluPXLvtPOHyYx6W97gMnzc8g',`nickname`='🌞小太阳🌞',`sex`='1',`language`='zh_CN',`city`='',`province`='',`headimgurl`='',`subscribe_time`='1441466036',`remark`='' WHERE `id` = 2542
```
SQL语句中包含*emoji*表情，在MySQL控制台中，执行该语句，发现该语句出现警告，并且没有正确执行。

## 解决方法
出现这种情况的原因是：普通的字符串或者表情都是占位3个字节，所以utf8足够用了，但是移动端的表情符号占位是4个字节，普通的utf8就不够用了，为了应对无线互联网的机遇和挑战、避免 emoji 表情符号带来的问题、涉及无线相关的 MySQL 数据库建议都提前采用 utf8mb4 字符集，这必须要作为移动互联网行业的一个技术选型的要点  

### 方法1：
方法1是修改数据库的字符集，这里只修改字段、表或数据库的字符集并不能从根本上解决问题，建议先备份整个数据库，然后再重新创建数据库。步骤如下(方法来自于stackoverflow)：
* [Step 1: Create a backup
](https://mathiasbynens.be/notes/mysql-utf8mb4#backup)
* [Step 2: Upgrade the MySQL server
](https://mathiasbynens.be/notes/mysql-utf8mb4#upgrade)
* [Step 3: Modify databases, tables, and columns
](https://mathiasbynens.be/notes/mysql-utf8mb4#databases-tables-columns)
* [Step 4: Check the maximum length of columns and index keys
](https://mathiasbynens.be/notes/mysql-utf8mb4#column-index-length)
* [Step 5: Modify connection, client, and server character sets
](https://mathiasbynens.be/notes/mysql-utf8mb4#character-sets)
* [Step 6: Repair and optimize all tables](https://mathiasbynens.be/notes/mysql-utf8mb4#repair-optimize)

<br />
### 方法2:
方法2是去掉字符中的emoji表情，实现该功能的函数:[https://github.com/tudouya/CodeLib/blob/master/PHP/function/removeEmoji.php](https://github.com/tudouya/CodeLib/blob/master/PHP/function/removeEmoji.php)

## 总结
又让博客断更了，惆怅(⊙o⊙)…

## 参考
* [http://stackoverflow.com/questions/7814293/how-to-insert-utf-8-mb4-characteremoji-in-ios5-in-mysql](http://stackoverflow.com/questions/7814293/how-to-insert-utf-8-mb4-characteremoji-in-ios5-in-mysql)
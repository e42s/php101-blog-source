---
title: Understanding MySQL key_len in Explain Statement
categories: X-StackOverflow
permalink: 'understanding,-mysql,-key_len,-in,-explain,-statement'
translate_title: understanding-mysql-key_len-in-explain-statement
date: 2015-08-12 17:25:59
tags:
description:
---
## 问题
According to MySQL website, the key_len column indicates the length of the key that MySQL decided to use. The length is NULL if the key column says NULL. Note that the value of key_len enables you to determine how many parts of a multiple-part key MySQL actually uses.  

Using an example from my previous question, I have an EXPLAIN SELECT statement that shows MySQL using an Index with key_len: 6. Below shows the composition of the index and columns used.  
```
`Type` char(1) NOT NULL,
`tn` char(1) NOT NULL DEFAULT 'l',
`act` tinyint(1) unsigned NOT NULL DEFAULT '0',
`flA` mediumint(6) unsigned NOT NULL DEFAULT '0',
KEY `Index` (`Type`, `tn`, `act`, `flA`)
```
So how does the value of key_len allows me to determine that my query uses the first three parts of a multiple-part key?

## 回答
The key_len specifies the number of bytes that MySQL uses from the key.  
Indexes are always used left_to_right. i.e. only the left-most part is used.  

The length of your fields is as follows:
```
1 byte             `Type` char(1) NOT NULL,
1 byte             tn char(1) NOT NULL DEFAULT 'l',
1 byte             act tinyint(1) unsigned NOT NULL DEFAULT '0',
3 bytes            flA mediumint(6) unsigned NOT NULL DEFAULT '0',
1+1+1+3 = 6 bytes  KEY `Index` (`Type`, `tn`, `act`, `flA`)
 key usage always starts here ---^^^^^
```
**If the key_len = 3 then it's using type+tn+act.**  
Note that Key_len = 4 is impossible in this configuration.

## 引用
* [http://stackoverflow.com/questions/7643491/understanding-mysql-key-len-in-explain-statement](http://stackoverflow.com/questions/7643491/understanding-mysql-key-len-in-explain-statement)
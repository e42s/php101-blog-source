---
title: MySQL中查询缓存的工作原理
categories: B-MySQL优化
permalink: the-working-principle-of-query-caching-in-mysql
translate_title: the-working-principle-of-query-caching-in-mysql
date: 2014-12-04 17:24:15
tags:
description:
---
## 概述
经常听到**查询缓存**这个词，也知道查询缓存能提高程序的运行效率，今天抽时间了解下MySQL中查询缓存的工作原理，记录下，不知道理解的对不对，如果有错误，请批评。

## 查询缓存的工作原理
> 查询缓存是MySQL 4.x.x以后的版本才有的新特性，如果MySQL版本的过低，那么查询缓存是不会工作的。   

当MySQL服务器接受到一个查询请求时，它会解析这个请求，从数据库/表中查询数据并返回到客户端浏览器。如果MySQL服务器接受到一个同样的查询请求时，服务器就会从查询缓存（节省了磁盘I/O开销和其他相关资源）中取出结果发送到客户端浏览器。  

**有一点需要注意的是，当数据表中的数据改变后，查询缓存中的相关条目也会被刷新(flush).**  

## 判断查询缓存是否工作
要查看查询缓存是否工作，有一个很简单的方法，MySQL为我们提供了这种工具，在MySQL命令行中，输入以下命令，即可查看查询缓存是否工作：
```
show status like 'Qcache%';
```  

## 总结
清楚了MySQL中查询缓存的工作方式，但是还有几个问题没有解决：
* MySQL的查询缓存存在哪里？内存中。
* 如何查看MySQL查询缓存中都缓存了哪些条目？
* 有没有相关的可视化工具？

## 参考
1.[How does query caching in MySQL works and how to find find out my MySQL query cache is working or not?](http://www.cyberciti.biz/tips/how-does-query-caching-in-mysql-works-and-how-to-find-find-out-my-mysql-query-cache-is-working-or-not.html)




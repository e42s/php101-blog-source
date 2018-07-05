---
title: MySQL的分页技术总结(转)
categories: B-MySQL
tags:
  - 分页
permalink: mysql-paging-technology-sumary-(turn)
translate_title: a-summary-of-mysql's-paging-technology
date: 2014-11-21 22:47:56
description:
---
## 概述
有朋友问: MySQL的分页似乎一直是个问题，有什么优化方法吗？  
网上看到赶集网XX推荐了一些分页方法,但似乎不太可行，你能点评一下吗？

## 方法总结
### 方法1: 直接使用数据库提供的SQL语句
* 语句样式: MySQL中,可用如下方法: SELECT * FROM 表名称 LIMIT M,N
* 适应场景: 适用于数据量较少的情况(元组百/千级)
* 原因/缺点: 全表扫描,速度会很慢 且 有的数据库结果集返回不稳定(如某次返回1,2,3,另外的一次返回2,1,3). Limit限制的是从结果集的M位置处取出N条输出,其余抛弃.


### 方法2: 建立主键或唯一索引, 利用索引(假设每页10条)
* 语句样式: MySQL中,可用如下方法: SELECT * FROM 表名称 WHERE id_pk > (pageNum*10) LIMIT M
* 适应场景: 适用于数据量多的情况(元组数上万)
* 原因: 索引扫描,速度会很快. 有朋友提出: 因为数据查询出来并不是按照pk_id排序的，所以会有漏掉数据的情况，只能方法3

### 方法3: 基于索引再排序
* 语句样式: MySQL中,可用如下方法: SELECT * FROM 表名称 WHERE id_pk > (pageNum*10) ORDER BY id_pk ASC LIMIT M
* 适应场景: 适用于数据量多的情况(元组数上万). 最好ORDER BY后的列对象是主键或唯一所以,使得ORDERBY操作能利用索引被消除但结果集是稳定的(稳定的含义,参见方法1)
* 原因: 索引扫描,速度会很快. 但MySQL的排序操作,只有ASC没有DESC(DESC是假的,未来会做真正的DESC,期待...).

### 方法4: 基于索引使用prepare（第一个问号表示pageNum，第二个？表示每页元组数）
* 语句样式: MySQL中,可用如下方法: PREPARE stmt_name FROM SELECT * FROM 表名称 WHERE id_pk > (？* ？) ORDER BY id_pk ASC LIMIT M
* 适应场景: 大数据量
* 原因: 索引扫描,速度会很快. prepare语句又比一般的查询语句快一点。

### 方法5: 存储过程类（最好融合上述方法4)
* 语句样式: 不再给出
* 适应场景: 大数据量.  作者推荐的方法
* 原因: 把操作封装在服务器，相对更快一些。

### 方法6: 反面方法
* 网上有人写使用 SQL_CALC_FOUND_ROWS。 没有道理，勿模仿

## 参考
原文地址：[MySQL的分页技术总结](http://blog.163.com/li_hx/blog/static/183991413201463110231904/)
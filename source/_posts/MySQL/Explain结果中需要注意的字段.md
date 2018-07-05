---
title: Explain结果中需要注意的字段
categories: B-MySQL优化
permalink: fields-to-be-noted-in-the-explain-results
translate_title: fields-to-be-paid-attention-to-in-the-explain-result
date: 2015-07-26 03:14:13
tags:
description:
---
## 概述
上一篇文章[Explain语句详解](http://www.php101.cn/2015/07/23/Explain%E8%AF%AD%E5%8F%A5%E8%AF%A6%E8%A7%A3/)详细介绍了使用Explain语句查看MySQL执行计划时，各字段的含义。  

今天看到了某大牛的一篇文章，文章里说明了EXPLAIN结果中哪些信息要引起关注，记录下来，方便以后查看。  

## type字段
如果有类型是 ALL 时，表示预计会进行全表扫描（full table scan）。通常全表扫描的代价是比较大的，建议创建适当的索引，通过索引检索避免全表扫描。此外，全索引扫描（full index scan）的代价有时候是比全表扫描还要高的，除非是基于InnoDB表的主键索引扫描。

## Extra字段
如果有出现 Using temporary 或者 Using filesort 则要多加关注。  

**Using temporary**，表示需要创建临时表以满足需求，通常是因为GROUP BY的列没有索引，或者GROUP BY和ORDER BY的列不一样，也需要创建临时表，建议添加适当的索引。  

**Using filesort**，表示无法利用索引完成排序，也有可能是因为多表连接时，排序字段不是驱动表中的字段，因此也没办法利用索引完成排序，建议添加适当的索引。  

**Using where**，通常是因为全表扫描或全索引扫描时（type 列显示为 ALL 或 index），又加上了WHERE条件，建议添加适当的索引。  

其他状态例如：Using index、Using index condition、Using index for group-by 则都还好，不用紧张。 

## 转载声明
对原文进行了重新排版。  

原文地址:  [[MySQL FAQ]系列 — EXPLAIN结果中哪些信息要引起关注](http://imysql.com/2015/06/14/mysql-faq-what-important-information-in-explain.shtml)
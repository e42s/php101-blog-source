---
title: '什么是PEAR,什么是PECL'
categories: A-PHP基础
tags:
  - PEAR
  - PECL
permalink: what-is-pear-and-what-is-pecl?
translate_title: what-is-pear-and-what-is-pecl
date: 2014-10-19 01:18:28
description:
---
## 概述
关于PEAR，PECL这两个东西，初学PHP的时候就知道，但是貌似用的人很少再加上以前也是在Windows下做开发，所以了解的不多，现在转到Mac了，就把这两个东西彻底弄弄清楚。

## 什么是PEAR
PEAR的全称是PHP Extension and Application Repository，是PHP的扩展库，采用PHP语言编写。所以我们可以直接下载，并在我们的代码中包含(include)扩展库。

## 什么是PECL
PECL的全称是PHP Extension Community Library，它也是PHP的扩展库。PECL的扩展都是采用C语言编写的，可以被载入PHP中，给PHP增加额外的功能，比如PHP的Mongo扩展。

## PEAR VS PECL
* PEAR采用PHP编写，PECL采用C语言编写
* PEAR也是一个PHP编码的标准

## 总结
以后安装PHP扩展库的时候，比如Mongo扩展，直接采用PECL安装，应该不会再出现版本不匹配的情况了~~

## 参考
* [What are differences between PECL and PEAR?](http://stackoverflow.com/questions/1385346/what-are-differences-between-pecl-and-pear)
* [Pecl vs Pear](http://board.phpbuilder.com/showthread.php?10339238-Pecl-vs-Pear)
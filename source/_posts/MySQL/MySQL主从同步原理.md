---
title: MySQL主从同步原理
categories: B-MySQL
permalink: mysql-master-slave-synchronization-principle
translate_title: mysql-master-slave-synchronization-principle
date: 2015-04-02 02:08:12
tags:
description:
---
## 概述
Mysql的Replication(复制)是一个异步的复制过程，从一个 Mysql instance(我们称之为 Master)复制到另一个Mysql instance(我们称之 Slave)。在 Master 与 Slave之间的实现整个复制过程主要由三个线程来完成，其中两个线程(Sql线程和IO线程)在 Slave 端，另外一个线程(IO线程)在Master端。

## 主从同步需求
要实现 MySQL 的 Replication ，首先必须打开 Master 端的BinaryLog(mysql-bin.xxxxxx)功能，否则无法实现。因为整个复制过程实际上就是Slave从Master端获取该日志然后再在自己身上完全顺序的执行日志中所记录的各种操作。打开 MySQL 的 Binary Log 可以通过在启动 MySQL Server 的过程中使用“—log-bin” 参数选项，或者在 my.cnf 配置文件中的 mysqld 参数组([mysqld]标识后的参数部分)增加“log-bin” 参数项。

## 主从同步过程
MySQL 复制的基本过程如下：
* Slave上面的IO线程连接上Master，并请求从指定日志文件的指定位置(或者从最开始的日志)之后的日志内容;
* Master接收到来自Slave的IO线程的请求后，通过负责复制的IO线程根据请求信息读取指定日志指定位置之后的日志信息，返回给Slave端的 IO线程。返回信息中除了日志所包含的信息之外，还包括本次返回的信息在Master端的Binary Log文件的名称以及在Binary Log中的位置;
* Slave的IO线程接收到信息后，将接收到的日志内容依次写入到 Slave 端的RelayLog文件(mysql-relay-bin.xxxxxx)的最末端，并将读取到的Master端的bin-log的文件名和位置记录到master-info文件中，以便在下一次读取的时候能够清楚的告诉Master“我需要从某个bin-log的哪个位置开始往后的日志内容，请发给我”。
* Slave的SQL线程检测到Relay Log中新增加了内容后，会马上解析该Log文件中的内容成为在Master 端真实执行时候的那些可执行的Query语句，并在自身执行这些Query。这样，实际上就是在Master端和Slave端执行了同样的Query，所以两端的数据是完全一样的。

实际上，在老版本中，MySQL 的复制实现在 Slave 端并不是由 SQL 线程和 IO线程这两个线程共同协作而完成的，而是由单独的一个线程来完成所有的工作。但是 MySQL的工程师们很快发现，这样做存在很大的风险和性能问题，主要如下：  
* 首先，如果通过一个单一的线程来独立实现这个工作的话，就使复制 Master 端的，BinaryLog日志，以及解析这些日志，然后再在自身执行的这个过程成为一个串行的过程，性能自然会受到较大的限制，这种架构下的Replication 的延迟自然就比较长了。
* 其次，Slave 端的这个复制线程从 Master 端获取 Binary Log 过来之后，需要接着解析这些内容，还原成Master 端所执行的原始 Query，然后在自身执行。在这个过程中，Master端很可能又已经产生了大量的变化并生成了大量的Binary Log 信息。如果在这个阶段 Master端的存储系统出现了无法修复的故障，那么在这个阶段所产生的所有变更都将永远的丢失，无法再找回来。这种潜在风险在Slave端压力比较大的时候尤其突出，因为如果 Slave压力比较大，解析日志以及应用这些日志所花费的时间自然就会更长一些，可能丢失的数据也就会更多。  

所以，在后期的改造中，新版本的 MySQL 为了尽量减小这个风险，并提高复制的性能，将 Slave端的复制改为两个线程来完成，也就是前面所提到的 SQL 线程和 IO线程。最早提出这个改进方案的是Yahoo!的一位工程师“JeremyZawodny”。通过这样的改造，这样既在很大程度上解决了性能问题，缩短了异步的延时时间，同时也减少了潜在的数据丢失量。  
当然，即使是换成了现在这样两个线程来协作处理之后，同样也还是存在 Slave数据延时以及数据丢失的可能性的，毕竟这个复制是异步的。只要数据的更改不是在一个事务中，这些问题都是存在的。

## 总结
初步了解了MySQL主从复制的原理。

## 参考
* [mysql主从同步原理](http://weibo.com/p/23041879a0eceb0102w5ef)
* [MySQL 主从复制资料汇总](http://blog.csdn.net/mchdba/article/details/44734597#0-tsina-1-63273-397232819ff9a47a7b7e80a40613cfe1)

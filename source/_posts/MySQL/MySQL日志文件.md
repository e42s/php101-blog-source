---
title: MySQL日志文件
categories: B-MySQL
permalink: mysql-log-file
translate_title: mysql-log-file
date: 2015-03-08 14:02:47
tags:
description:
---
## 概述
日志文件是MySQL数据库的重要组成部分。MySQL有几种不同的日志文件，通常包括错误日志文件，二进制日志，通用日志，慢查询日志，等等。这些日志可以帮助我们定位mysqld内部发生的事件，数据库性能故障，记录数据的变更历史，用户恢复数据库等等。本文主要描述MySQL的各种日志文件。


## MySQL日志文件分类
* 错误日志(Error Log)
* 二进制日志(Binary Log & Binary Log Index)
* 通用查询日志(query log)
* 慢查询日志(slow query log)
* Innodb的在线 redo 日志(innodb redo log)
* 更新日志(update log)

## 错误日志
错误日志记录了MyQL Server运行过程中所有较为严重的警告和错误信息,以及MySQLServer 每次启动和关闭的详细信息。在默认情况下,系统记录错误日志的功能是关闭的, 错误信息被输出到标准错误输出(stderr) ,如果要开启系统记录错误日志的功能,需要在启动时开启-log-error 选项。错误日志的默认存放位置在数据目录下,以hostname.err 命 名。但是可以使用命令:--log-error[=file_name],修改其存放目录和文件名。  

为了方便维护需要, 有时候会希望将错误日志中的内容做备份并重新开始记录, 这候时 就可以利用 MySQL 的 FLUSH LOGS 命令来告诉 MySQL 备份旧日志文件并生成新的日志文件。 备份文件名以“.old”结尾。

## 二进制日志
二进制日志,也就是我们常说的 binlog,也是 MySQL Server 中最为重要的日志之一。当我们通过“--log-bin[=file_name]”打开了记录的功能之后,MySQL 会将所有修改数据 库数据的 query 以二进制形式记录到日志文件中。 当然, 日志中并不仅限于query 语句这么 简单, 还包括每一条query 所执行的时间, 所消耗的资源, 以及相关的事务信息,bi所nl以og 是事务安全的。  

和错误日志一样,binlog 记录功能同样需要“--log-bin[=file_name]”参数的显式指 定才能开启, 如果未指定file_name, 则会在数据目录下记录为mysql-bin.****** (*代表0~ 9 之间的某一个数字,来表示该日志的序号)。  

binlog 还有其他一些附加选项参数:“--max_binlog_size”设置 binlog 的最大存储上限,当日志达到该上限时,MySQL 会 重新创建一个日志开始继续记录。不过偶尔也有超出该设置的binlog 产生,一般都是因为 在即将达到上限时,产生了一个较大的事务,为了保证事务安全,MySQL 不会将同一个事务 分开记录到两个 binlog 中。“--binlog-do-db=db_name”参数明确告诉 MySQL,需要对某个(db_name)数据库记 录 binlog,如果有了“--binlog-do-db=db_name”参数的显式指定,MySQL 会忽略针对其他 数据库执行的 query,而仅仅记录针对指定数据库执行的query。“--binlog-ignore-db=db_name”与“--binlog-do-db=db_name”完全相反, 它显式指 定忽略某个(db_name)数据库的binlog 记录,当指定了这个参数之后,MySQL 会记录指定 数据库以外所有的数据库的 binlog。“--binlog-ignore-db=db_name”与“--binlog-do-db=db_name”两个参数有一个共同 的概念需要大家理解清楚,参数中的db_name 不是指 query 语句更新的数据所在的数据库, 而是执行 query 的时候当前所处的数据库。 不论更新哪个数据库的数据,MySQL 仅仅比较当 前连接所处的数据库(通过use db_name切换后所在的数据库)与参数设置的数据库名,而 不会分析 query 语句所更新数据所在的数据库。mysql-bin.index 文件(binary log index)的功能是记录所有Binary Log的绝对路 径,保证 MySQL 各种线程能够顺利的根据它找到所有需要的Binary Log 文件。  

## 通用查询日志
查询日志记录 MySQL 中所有的 query,通过“--log[=fina_name]”来打开该功能。由于记录了所有的 query,包括所有的 select,体积比较大,开启后对性能也有较大的影响, 所以请大家慎用该功能。 一般只用于跟踪某些特殊的sql 性能问题才会短暂打开该功能。默认的查询日志文件名为 hostname.log。

## 慢查询日志
顾名思义,慢查询日志中记录的是执行时间较长的 query,也就是我们常说的 slowquery,通过设 --log-slow-queries[=file_name]来打开该功能并设置记录位置和文件名, 默认文件名为 hostname-slow.log,默认目录也是数据目录。  

慢查询日志采用的是简单的文本格式, 可以通过各种文本编辑器查看其中的内容。中其 记录了语句执行的时刻,执行所消耗的时间,执行用户,连接主机等相关信息。MySQL 还提 供了专门用来分析满查询日志的工具程序mysqlslowdump, 用来帮助数据库管理人员解决可 能存在的性能问题。

## Innodb的在线redo日志
Innodb 是一个事务安全的存储引擎, 其事务安全性主要就是通过在线redo 日志和记录在表空间中的 undo 信息来保证的。redo 日志中记录了 Innodb 所做的所有物理变更和事务 信息, 通过redo 日志和 undo 信息, Innodb 保证了在任何情况下的事务安全性。Innodb 的redo 日志同样默认存放在数据目录下, 可以通过innodb_log_group_home_dir来更改设置日志的 存放位置,通过 innodb_log_files_in_group 设置日志的数量。

## 更新日志
更新日志是 MySQL 在较老的版本上使用的,其功能和 binlog 基本类似,只不过不是以二进制格式来记录而是以简单的文本格式记录内容。自从 MySQL增加了binlog功能之后, 就很少使用更新日志了。从版本5.0 开始,MySQL 已经不再支持更新日志了。  

## 总结
本篇文章整理了MySQL中的各种日志文件，每种日志文件都用其特定的用途，这些会在以后的文章中进行描述。

## 参考
* [MySQL 通用查询日志(General Query Log)](http://blog.csdn.net/leshami/article/details/39779225#0-tsina-1-11168-397232819ff9a47a7b7e80a40613cfe1)
* [MySQL 日志](http://blogread.cn/it/article/3472?f=wb)

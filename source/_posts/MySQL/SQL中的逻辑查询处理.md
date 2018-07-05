---
title: SQL中的逻辑查询处理
categories: B-MySQL
permalink: logical-query-procesing-in-sql
translate_title: logical-query-processing-in-sql
date: 2014-09-10 17:29:09
tags:
---
##	概述
相比其他编程语言(PHP、C++等)，SQL编程语言有许多独立的特性，比如说集合的思维方式, 元素查询时的逻辑执行顺序以及三值逻辑([Three-valued logic](http://en.wikipedia.org/wiki/Three-valued_logic)).本篇文章提到的一些逻辑查询处理效率是非常低的，但是有一点我们需要记住的是，SQL语句逻辑查询处理和物理查询处理可能会有很大的不同。
## 逻辑查询和物理查询
在MySQL中，对一条查询语句生成真正执行计划(execution plan)的组件是查询优化器(query optimizer)。查询优化器决定了访问数据表的顺序，访问方法和索引的使用以及应用哪种连接算法等等。查询优化器会生成许多执行计划，然后选择消耗资源最少、性能最高的那条计划。相对于物理查询，逻辑查询有非常明确的顺序。换句话说，查询优化器在执行物理查询时，选择了一条捷径。当然，尽管查询优化器在物理查询阶段选择了一条捷径，但是它是在保证这条捷径能与逻辑查询获取相同结果的前提下进行的。  

总的来说，逻辑查询处理表示执行查询应该产生什么样的结果，而物理查询代表MySQL数据库是如何得到该结果的。两种查询的方法可能完全不同，但是得到的结果必然是相同的。
## 逻辑查询处理的各个阶段
逻辑查询处理包含以下几个阶段：  
```
(8) SELECT (9) DISTINCT <select_list>   
(1) FROM <left_table>  
(3) <join_type> JOIN <right_table>  
(2) 	ON <join_condition>  
(4) WHERE <where_condition>   
(5) GROUP BY <group_by_list>   
(6) WITH {CUBE | ROLLUP}  
(7) HAVING <having_condition>   
(10) ORDER BY <order_by_list>
(11) LIMIT <limit_number>
```
上面代码中的序号表示SQL语句的执行顺序，可以看到一共有11个步骤。SQL编程语言不同于其他语言的一个显著方面就是代码的执行顺序。在大多数编程语言中，代码按照编写的顺序从上到下执行。但是，在SQL语言中，最先被执行的是FROM子句，虽然SELECT语句被写在最前面，但它几乎是在最后才被执行的。 
 
在逻辑查询处理的11个步骤中，每个步骤都会产生一张虚拟表，产生的虚拟表会作为下一个步骤的输入。每个步骤产生的虚拟表都是不可见的, 只有最后一步产生的表才会被返回。上述的11个步骤中，如果有哪个步骤中的SQL语句没有被使用，那么逻辑查询就会跳过该步骤，直接执行下一个步骤。  

下面会单独介绍逻辑查询处理的每一个步骤。
## 逻辑查询处理各个步骤详解
### FROM
对FROM子句中的左表(<left_table>)和右表(<right_table>)执行笛卡尔积(Cartesian product)，产生虚拟表VT1。
> 产生笛卡尔积的过程其实就是执行交叉连接(cross join)。

<br />
### ON
对虚拟表VT1应用ON过滤器, 只有符合<join_condition>过滤条件的行才会被插入到虚拟表VT2中。
<br />
### OUTER(join)
如果指定了外连接OUTER JOIN(相对于交叉连接和内连接来说)，那么保留表中未匹配的行作为外部行添加到虚拟表VT2中，产生虚拟表VT3。如果FROM字句包含两个以上的表，则对上一个结果产生的结果表VT3和下一个表重复执行步骤(1)～步骤(3),直到处理完所有的表为止。
<br />
### WHERE
对虚拟表VT3应用WHERE过滤条件，只有符合<where_condition>过滤条件的行才被插入到虚拟表VT4中。
<br />
### GROUP BY
根据GROUP BY子句中的列，对虚拟表VT4中的行进行分组，产生虚拟表VT5。
<br />
### CUBE | ROLLUP
对虚拟表VT5进行CUBE或ROLLUP操作，产生虚拟表VT6。
<br />
### HAVING
对虚拟表VT6应用HAVING过滤器，只有符合<having_condition>过滤条件的行才被插入到虚拟表VT7中。
<br />
### SELECT
执行SELECT操作，产生虚拟宝VT8。
<br />
### DISTINCT
去除重复数据，产生虚拟表VT9。
<br />
### ORDER BY
将虚拟表VT9中的行按照<order_by_list>进行排序操作，产生虚拟宝VT10。
<br />
### LIMIT
取出指定行的记录，产生虚拟表VT11，并返回给用户。
<br />

## 总结
本篇文章概括介绍了逻辑查询处理的各个步骤，希望大家对SQL语句能有更进一步的认识。在下一篇文章中，会通过一个实例向大家更详细的介绍各个步骤，也希望通过实例加深大家对本篇文章的理解。


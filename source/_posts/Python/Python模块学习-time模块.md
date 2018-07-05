---
title: 'Python模块学习:time模块'
categories: F-Python
permalink: 'python-module-learning:-time-module'
translate_title: 'python-module-learning:-time-module'
date: 2016-05-30 14:12:39
tags:
description:
---
## 概述
Python的time模块依赖于底层的C库函数，用来操作日期和时间。由于time模块依赖于底层的C，所以它在不同的系统平台上有不同的特性(如支持的最大日期)。   

实际上，计算机只会维护一个挂钟时间(wall clock time)，这个时间是从某个固定时间起点到现在的时间间隔。时间起点的选择与计算机相关，但一台计算机的话，这一时间起点是固定的。其它的日期信息都是从这一时间计算得到的。此外，计算机还可以测量CPU实际上运行的时间，也就是处理器时间(processor clock time)，以测量计算机性能。当CPU处于闲置状态时，处理器时间会暂停。

## 挂钟时间(wall clock time)
**time()**是time模块的一个核心函数，该函数返回一个从1970年1月1日以来的秒数，这是一个浮点数。  

当我们用来比较两个日期时，用浮点数来比较时间是比较好的。但是，用浮点数来表示时间，这种格式读起来不太友好,如果用来记录日志或打印使用**ctime()**比较好。

ctime()接受一个时间戳作为参数，默认值为time.time()。

使用示例:  

```
In [14]: time.ctime()
Out[14]: 'Mon May 30 14:29:10 2016'

In [15]: time.time()
Out[15]: 1464589753.648325
```

## 处理器时间
time()函数返回的是挂钟时间，与之相对的**clock()**函数返回的是处理器时间。clock()函数的返回值通常被用做测试、基本测试等。

使用示例:
```
sleep(3)
time.clock()
```

## struct_time
将时间存储为秒数在某些场景下非常有用，但是有时我们需要访问一个日期的单独部分(如年、月、日、时)。time模块定义了struct_time用来存储日期的不同部分，方便我们来访问。   

使用示例:  

```
In [16]: time.localtime()

Out[16]: time.struct_time(tm_year=2016, tm_mon=5, tm_mday=30, tm_hour=14, tm_min=35, tm_sec=16, tm_wday=0, tm_yday=151, tm_isdst=0)

In [17]: time.gmtime()
Out[17]: time.struct_time(tm_year=2016, tm_mon=5, tm_mday=30, tm_hour=6, tm_min=35, tm_sec=21, tm_wday=0, tm_yday=151, tm_isdst=0)
```
gmtime([sec])，可选的参数sec表示从1970-1-1以来的秒数。其默认值为time.time()，函数返回time.struct_time类型的对象。  

localtime()与gmtime()类似。  

mktime()函数用接受一个struct_time类型的参数并将它转换为秒数。 


## 解析时间
函数**strptime()**和**strftime()**用来将时间在字符串和struct_time之间相互转换。 

strptime()按指定格式解析一个表示时间的字符串，返回struct_time对象。该函数原型为：time.strptime(string, format)，两个参数都是字符串。  

strftime将日期转换为字符串表示，它的函数原型为：time.strftime(format[, t])。参数format是格式字符串，格式字符串的知识可以参考：[time.strftime](https://docs.python.org/2.7/library/time.html#time.strftime)

使用示例:  

```
In [19]: time.strptime(time.ctime())

Out[19]: time.struct_time(tm_year=2016, tm_mon=5, tm_mday=30, tm_hour=14, tm_min=41, tm_sec=33, tm_wday=0, tm_yday=151, tm_isdst=-1)
```

## 参考
* [Python模块学习：time 日期时间处理 ](http://python.jobbole.com/81550/)
* [Python 快速教程（标准库02）：时间与日期 (time, datetime包)](http://python.jobbole.com/82690/)

<br />
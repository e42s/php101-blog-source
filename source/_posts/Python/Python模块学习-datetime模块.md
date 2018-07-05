---
title: 'Python模块学习:datetime模块'
categories: F-Python
permalink: 'python-module-learning:-datetime-module'
translate_title: 'python-module-learning:-datetime-module'
date: 2016-05-30 15:31:07
tags:
description:
---
## 概述
datetime包是基于time包的一个高级包， 为我们提供了多一层的便利。  

datetime可以理解为date和time两个组成部分。date是指年月日构成的日期(相当于日历)，time是指时分秒微秒构成的一天24小时中的具体时间(相当于手表)。你可以将这两个分开管理(datetime.date类，datetime.time类)，也可以将两者合在一起(datetime.datetime类)。

## time
time的值用time类表示。time类有hour、minute、second和microsecond四个属性。它也可以包含时区信息。实例化一个time类的参数是可选的，其默认值为0. 

使用示例：  

```
In [55]: t = datetime.time(15, 30, 32)

In [56]: t.hour
Out[56]: 15

In [57]: t.minute
Out[57]: 30

In [59]: t.second
Out[59]: 32

In [60]: t.microsecond
Out[60]: 0
```
一个time类的实例仅包含时间信息，并没有包含日期信息。

time类的min和max属性表示了一天时间允许的范围，如下： 

```
In [63]: datetime.time.min
Out[63]: datetime.time(0, 0)

In [64]: datetime.time.max
Out[64]: datetime.time(23, 59, 59, 999999)
```

## date
日期的值用date类表示。date类的有year、month和day属性。使用date类的today()方法可以很容易获得当天的时间。  

使用示例：  
  
```  
In [69]: today = datetime.date.today()

In [70]: today.ctime()
Out[70]: 'Mon May 30 00:00:00 2016'

In [71]: today.timetuple()
Out[71]: time.struct_time(tm_year=2016, tm_mon=5, tm_mday=30, tm_hour=0, tm_min=0, tm_sec=0, tm_wday=0, tm_yday=151, tm_isdst=-1)

In [72]: today.toordinal()
Out[72]: 736114

In [73]: today.year
Out[73]: 2016

In [74]: today.month
Out[74]: 5

In [75]: today.day
Out[75]: 30
```

date类有两个从整型数据创建实例的方法：**fromordinal()**和**fromtimestamp()**。   

同time类一样，date类也有min、max、resolution三个属性，用来表示可以使用的日期范围。 

另一种常见date类实例的方法是使用一个date类实例的**replace()**方法。  

使用示例:  

```  
In [79]: t = datetime.date(2016, 05, 30)

In [80]: t
Out[80]: datetime.date(2016, 5, 30)

In [81]: t.replace(year=2017)
Out[81]: datetime.date(2017, 5, 30)
```

replace()方法并不是唯一一种用来创建过去或将来时间的方法。我们可以使用**datetime**类中的** timedelta**类执行基本的日期运算。  

使用示例： 
```
datetime.timedelta(microseconds=1)
datetime.timedelta(days=1)  
```

## 日期运算
日期运算使用基本的日期运算。  
使用示例： 
 
```
In [83]: today = datetime.date.today()

In [85]: two_day = datetime.timedelta(days=2)

In [86]: today - two_day
Out[86]: datetime.date(2016, 5, 28)
```

## datetime
使用datetime类来组合date和time。和date类类似，datetime类有几个方便的方法创建实例。  

使用实例: 

```
In [89]: datetime.datetime.now()
Out[89]: datetime.datetime(2016, 5, 30, 16, 17, 53, 855694)

In [90]: datetime.datetime.today()
Out[90]: datetime.datetime(2016, 5, 30, 16, 18, 22, 532185)

In [91]: datetime.datetime.utcnow()
Out[91]: datetime.datetime(2016, 5, 30, 8, 18, 38, 980115)
```

datetime同样也有**fromordinal()**和**fromtimestamp()**方法。

## 格式化
datetime类提供了格式化日期和时间的方法: **strftime()**和**strptime()**。

使用示例： 

```
In [92]: today = datetime.datetime.now()

In [94]: today.s
today.second    today.strftime  today.strptime

In [94]: today.str
today.strftime  today.strptime

In [94]: today.strftime("%Y-%m-%d %H:%M:%S")
Out[94]: '2016-05-30 16:24:41'
```

## 总结
datetime类和time类各有各的优势，根绝不同的场景选择不同的类来使用。  

<br />
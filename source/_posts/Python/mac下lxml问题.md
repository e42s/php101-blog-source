---
title: mac下lxml问题
categories: F-Python
permalink: lxml-problem-under-mac
translate_title: lxml-problem-under-mac
date: 2016-08-11 10:42:35
tags:
description:
---
## 概述
在mac下安装scrapy爬虫框架时，经常会遇到lxml出错的问题，每次安装都需要折腾一次，具体出错的原因不知道，此篇文章仅记录解决方案。 


## lxml问题解决
在mac下使用lxml会出现如下问题:

```
>>> from lxml import etree

Traceback (most recent call last):
File "", line 1, in 
ImportError: dlopen(/Users/fulvio/plone/gisweb/lib/python2.7/site-packages/lxml/etree.so, 2): Symbol not found: _xmlBufContent
Referenced from: /Users/fulvio/plone/gisweb/lib/python2.7/site-packages/lxml/etree.so
Expected in: flat namespace
in /Users/fulvio/plone/gisweb/lib/python2.7/site-packages/lxml/etree.so
```

解决方法:  

安装lxml **2.2.8**版本，如下:

```
pip install lxml==2.2.8
```


## 总结
慢慢来咕~~(╯﹏╰)b

<br />
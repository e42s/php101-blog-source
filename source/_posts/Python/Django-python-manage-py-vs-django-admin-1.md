---
title: 'Django:[python manage.py] vs [django-admin]'
categories: F-Django
permalink: 'django:[python-manage.py]-vs-[django-admin]'
translate_title: 'django:[python-manage.py]-vs-[django-admin]'
date: 2016-04-10 21:19:28
tags:
description:
---
## 概述
接触Django和Python快一年的时间了，之前一直是上手就做，一直感觉没有太系统的消化过Python知识，这次要细化一下之前的学习。    


## manage.py和django-admin的区别
初学django的肯定都知道django新建一个项目是使用命令`django-amdin startproject projectname`，但是在创建项目以后新建django APP的时候有两种方式可以使用:`django-admin startapp appname`和`python manange.py startapp appname`。  

之前就一直比较疑惑这两种方式的区别，貌似django-admin能做的事情，manage.py也能做，今天查了以下手册以及看了几个SO上的几个问题，感觉清晰了很多，django文档中是这样解释的：  

```
django-admin is Django’s command-line utility for administrative tasks. This document outlines all it can do.

In addition, manage.py is automatically created in each Django project. manage.py does the same thing as django-admin but takes care of a few things for you:

* It puts your project’s package on sys.path.
* It sets the DJANGO_SETTINGS_MODULE environment variable so that it points to your project’s settings.py file.
```

大致翻译过来是这样的：  

```
manage.py在每个django项目中都会被自动创建。manage.py和django-admin的功能基本一样，但是manage.py为用户考虑了以下几件事情：
* 它将项目的包放在了sys.path路径中
* 它设置了DJANGO_SETTINGS_MODULE环境变量，因此可以找到项目的配置文件。
```

同时，参考了SO上的几个问题，得出以下结论：  

**manage.py是django-admin的一个包装，如非必要，请除了创建项目时使用django-admin，其余情况请尽量使用manage.py**


## 总结
进步不要多，每天一点点(⊙o⊙)…

## 参考：
* [django文档](https://docs.djangoproject.com/en/1.8/ref/django-admin/#django-admin-and-manage-py)
* [“python manage.py runserver” vs “django-admin runserver”](http://stackoverflow.com/questions/32199621/python-manage-py-runserver-vs-django-admin-runserver)

<br />

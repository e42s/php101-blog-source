---
title: Mac下pip install安装模块遇到的几个问题
categories: F-Python
permalink: several-problems-encountered-in-the-instalation-module-of-pip-instal-under-mac
translate_title: several-problems-encountered-in-the-pip-install-installation-module-under-mac
date: 2016-06-14 10:29:05
tags:
description:
---
## 概述
在Mac下使用了MAMP这个PHP集成环境，之后在装Python模块的时候就各种报错，各种找不到路径。对于Python的模块来说，一直使用的virtualenv来管理Python的模块，今天使用了下virtualenvwrapper，需要重装所有的模块，又遇到了以前的问题，为了防止持续踩坑，写篇文章记录下。

## MySQL-python模块
在安装`MySQL-python`模块的时候，经常会遇到找不到`my_config.h`这个问题，解决方法如下： 
 
(1)安装`mysql-connector-c`  

```
brew install mysql-connector-c
```
(2)由于使用的是MAMP集成环境，所以安装以后，需要将`mysql-connector-c`中的文件链接到MAMP的`include`目录,执行如下命令: 

```
cd /Applications/MAMP/Library/include
ln -s /usr/local/Cellar/mysql-connector-c/6.1.6/include/* .

cd /Applications/MAMP/Library/lib
ln -s /usr/local/Cellar/mysql-connector-c/6.1.6/lib/* .
```

## Scrapy安装
在安装Scrapy 1.1.0的时候，各个模块都会顺利安装，但是在运行爬虫的时候，会报一个`lxml`相关的额问题，这个地方可能是两个问题引起的，一个是缺少lxml底层库，一个是版本问题，简单粗暴的统一解决下：
(1) 安装lxml底层库

```
brew install libxml2
brew install libxslt
brew link libxml2 --force
brew link libxslt --force
```

(2) 安装lxml模块
Scrapy 1.1.0版本貌似不支持lxml最新版本，在安装Scrapy的时候，会直接安装lxml这个依赖，所以需要先卸载，然后重新安装lxml 2.2.8版本, 如下： 
 
```
pip uninstall lxml
pip install lxml==2.2.8
```

## 总结
前辈大牛们真的没骗人，学习Python，10%的时间是在编码，90%的时间是在。。。。(⊙o⊙)…

<br />
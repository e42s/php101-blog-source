---
title: Git忽略已跟踪的文件
categories: Y-Git
permalink: git-ignores-files-that-have-ben-traced
translate_title: git-ignores-the-files-that-have-been-tracked
date: 2014-08-29 17:39:20
tags:
---
## 概述
在使用Git的时候，有时候我们需要对某个文件不进行版本控制，也就是忽略对某个文件的跟踪，如缓存文件、配置文件等。  
但是在Git的版本控制系统中，如果已经对某个文件进行了版本控制，那么再忽略掉此文件的时候，会删除掉远程服务器的文件，这显然与我们的意愿不符，Google了好久，找到了下面这个办法。
## 命令
### 我们在Clone完某个项目后，运行如下命令，本地Git库即可忽略对某个指定文件的版本记录:
```
　　git update-index --assume-unchanged filename [要忽略的文件的名称]
```
### 使本地git库重新跟踪指定文件，运行下面的命令:
```
　　git update-index –no-assume-unchanged filename [要重新跟踪的文件的名称]
```
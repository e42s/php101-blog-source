---
title: Git三种克隆方式的不同之处
categories: Y-Git
tags: git
permalink: git-diferences-betwen-the-thre-ways-of-cloning
translate_title: differences-in-the-three-cloning-methods-of-git
date: 2015-03-22 18:27:27
description:
---
## 概述
今天在研究Git实现项目自动部署的时候，参考的文章中出现了`git clone --mirror`的使用方式，之前见过`git clone --bare`的使用方式，但是一直没有去研究，今天试着研究下git克隆版本库时，不同版本库代表的意思。

## 不同之处
### 不带参数
```
git clone <repository> <directory>
```
将<repository>指向的版本库创建一个克隆到<directory>目录。目录<directory>相当于克隆版本库的工作区，文件都会检出，版本库位于工作区的.git目录中
<br />
### --bare参数
```
git clone --bare <repository> <directory.git>
```
<br />
### --mirror参数
```
git clone --mirror <repository> <directory.git>
```
<br />
### 三种方式的异同
`--bare`参数和`--mirror`参数创建的克隆版本库都不包含工作区，直接就是版本库的内容，这样的版本库称为**裸版本库**。一般约定俗成裸版本库的目录名以.git做后缀，所以上面示例中将克隆出来的裸版本库目录名写作<directory.git>。区别在于`--mirror`参数克隆出来的裸版本对上游版本库进行了注册，这样可以在裸版本库中使用`git fetch`命令和上游版本库进行持续同步。<br />

不使用--bare或--mirror创建出来的克隆包含工作区，这样就会产生两个包含工作区的版本库，这两个版本库对等。这两个工作区本质上没有区别，往往提交在一个版本A中进行，另一个B作为备份。只能从B执行git pull命令从A中拉回新的提交实现版本库同步，而不能从版本库A向版本库B执行git push推送操作。<br />

还可以通过git init的方式创建裸版本库，需要加--bare参数。<br />

当执行git push命令时，如果没有设定推送的分支，而且当前分支也没有注册到远程的某个分支，将检查远程分支是否有和本地相同的分支名（如master），如果有，则推送，否则报错。

## 总结
大体弄明白了Git三种不同得克隆方式。

## 参考
* [Git克隆 ](http://blog.csdn.net/agul_/article/details/7843678)
* [git clone各种类型之间的区别](http://ericyou.iteye.com/blog/1727540)
* [准备更换git托管，如何迁移原git仓库](http://segmentfault.com/q/1010000000124379)

---
title: 'Linux命令:export'
categories: D-Linux命令
tags:
  - 环境变量
permalink: 'linux-comand:-export'
translate_title: 'linux-command:-export'
date: 2014-10-19 02:41:28
description:
---
## 概述
export命令是Linux shell内建(BUILTINS)的命令之一,也就是说它是shell的一部分。export命令非常容易使用，因为它只有三个常用选项。一般来说，export命令标记了一个可以被新创建(forked)的子进程输出的变量，因此它允许子进程继承所有被标记的命令。  
这么说可能比较模糊，下面用具体的例子说明export命令。

## export常用选项
### -p
列出当前shell中所有被输出(exported)的名字
### -n
从输出列表中删除名字
### -f
名字转函数

## export基础(Export basics)
看下面的例子：
```
tudouya@Chain:~$ a=www.php101.cn
tudouya@Chain:~$ echo $a
www.php101.cn
tudouya@Chain:~$ bash
tudouya@Chain:~$ echo $a
tudouya@Chain:~$ 
```
* 第一行：创建了一个变量a，并赋值为"wwww.php101.cn"
* 第二行：使用echo命令输出了变量a的内容，得到了变量的内容
* 第四行：创建了一个子bash shell
* 第五行：使用echo命令输出变量a，但是结果为空

从上面的例子可以看出，任何从父进程fork的子进程都没有继承父进程中的变量。export在这种情况下就非常有用。我们对上面的例子做下修改：
```
tudouya@Chain:~$ a=www.php101.cn
tudouya@Chain:~$ echo $a
www.php101.cn
tudouya@Chain:~$ export a
tudouya@Chain:~$ bash
tudouya@Chain:~$ echo $a
www.php101.cn
tudouya@Chain:~$ 
```
在这个例子中，我们增加了一行命令`export a`,这样在子当前bash的子bash中就可以获得父进程的变量，这就是export命令的作用。  
有一点需要注意的是，要在子bash中输出父bash的变量，那么子bash必须是从export所在的父bash中创建的，否则是不生效的。

## 使用export命令
从上面的例子我们简单了解了export命令的基础，这里详细解释下export命令的使用。当不加任何选项和参数使用export命令的时候，export会输出所有标记的子进程可以使用的名字。示例：
```
tudouya@Chain:~$ export
declare -x CLUTTER_IM_MODULE="xim"
declare -x COLORTERM="gnome-terminal"
declare -x COMPIZ_BIN_PATH="/usr/bin/"
declare -x COMPIZ_CONFIG_PROFILE="ubuntu"
declare -x DBUS_SESSION_BUS_ADDRESS="unix:abstract=/tmp/dbus-0O9QY0AN1x"
declare -x DEFAULTS_PATH="/usr/share/gconf/ubuntu.default.path"
declare -x DESKTOP_SESSION="ubuntu"
declare -x DISPLAY=":0"
declare -x GDMSESSION="ubuntu"
declare -x GDM_LANG="en_US"
declare -x GNOME_DESKTOP_SESSION_ID="this-is-deprecated"
declare -x GNOME_KEYRING_CONTROL="/run/user/1000/keyring-eDeDFG"
declare -x GNOME_KEYRING_PID="2293"
..........
```
像前面的示例一样，如果我们想标记一个变量，只需定义一个变量，然后在export命令中使用该变量名即可。示例：
```
tudouya@Chain:~$ blog=aaa
tudouya@Chain:~$ export blog
tudouya@Chain:~$ export |grep blog
declare -x blog="aaa"
```
export命令最常用的地方就是为PATH变量增加路径:
```
export PATH=$PATH:/usr/local/bin
```
这个例子为PATH变量增加了`/usr/local/bin`路径。

## 总结
export命令还有很多高级用法，但是目前来说理解不了，以后再深入学习~~

## 参考
* [Learning Linux Commands: export](http://how-to.linuxcareer.com/learning-linux-commands-export)
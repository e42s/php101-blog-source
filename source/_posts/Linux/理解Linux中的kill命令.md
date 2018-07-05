---
title: 理解Linux中的kill命令
categories: D-Linux命令
permalink: understand-the-kil-comand-in-linux
translate_title: understand-the-kill-command-in-linux
date: 2015-10-08 20:07:06
tags:
description:
---
## 概述
用了Linux命令这么久，经常看到别人直接使用`kill -9`命令去结束一个进程，以前也只知道`9`代表一个信号量，抽空对Linux中的kill命令做个调查。

## kill命令
当执行kill命令的时候，实际上是向系统发送了一个信号，该信号指示操作系统结束一个进程。在Linux系统中，大概有60多个信号可以使用，但是通常使用的只有两个SIGTERM(15)和SIGKILL(9)。  

可以使用如下命令查看所有的信号：
```
kill -l
```
* SIGTERM,该信号指示进程停止运行。该信号可以被忽略。使用该信号的时候，被结束的进程可以平滑的停止，这意味着进程有时间去关闭log文件，释放资源等。换句话说，进程不是被强制停止的。
* SIGKILL,该信号强制进程停止。进程不能忽略该信号。

kill命令的语法为:
```
kill [signal or option] PID(s)
```
默认的信号为`SIGTERM`,当该信号不起作用时，可以使用如下命令强制停止进程：
```
kill SIGKILL PID
```
或者
```
kill -9 PID
```
## pkill
`pkill`命令允许使用正则表达式扩展或者其他可匹配的场景。使用`pkill `命令，可以直接使用进程名而不是使用进程pid来结束进程。如，想停止火狐浏览器进程，可以使用如下命令：
```
pkill firefox
```
或者
```
pkill fire
```
为了避免停止错误的进程，可以使用`pgrep -l [process name]`来列车匹配的进程。

## killall
killall命令同样使用进程名称而不是进程pid来停止进程，它会停止所有相同名称的进程。如：
```
killall firefox
```

## xkill
xkill是以图形方式结束进程的命令。

## 参考
* [Mastering the “Kill” Command in Linux](https://www.maketecheasier.com/kill-command-in-linux/)
* [Understanding the kill command, and how to terminate processes in Linux](http://bencane.com/2014/04/01/understanding-the-kill-command-and-how-to-terminate-processes-in-linux/)
* [https://en.wikipedia.org/wiki/Unix_signal](https://en.wikipedia.org/wiki/Unix_signal)
* [https://www.quora.com/What-is-the-difference-between-the-SIGINT-and-SIGTERM-signals-in-Linux](https://www.quora.com/What-is-the-difference-between-the-SIGINT-and-SIGTERM-signals-in-Linux)
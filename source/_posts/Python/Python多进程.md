---
title: Python多进程(fork)
categories: F-Python
permalink: python-multi-proces-(fork)
translate_title: python-multi-process-(fork)
date: 2015-08-21 10:56:38
tags:
description:
---
## 概述
多进程能增加程序的并发处理能力，Python提供了多种操作多进程的方式。之前也一直对多进程的概念有点模糊，所以对这部分内容进行了整理和深入学习。

## fork
在类Unix系统中，python的`os`模块内置了fork 函数用以创建子进程。fork()函数会创建当前进程的副本，并作为当前进程的子进程来运行。调用fork()函数时，该函数会**返回两次**。

## 进程区分
在程序中调用fork()函数时，该函数会返回两次。所以程序中的逻辑会被执行两次，通常可以通过fork()的返回值来区分父进程和子进程。**在父进程中，fork()返回的是子进程的进程ID，一般为正数。在子进程中，fork()的返回值为0。如果返回值为负数，表示fork()进程失败。**所以，我们可以通过fork的返回值来判断当前进程是子进程还是父进程。  
示例代码：
```
import os
pid = os.fork()

if pid == 0:
    fout = open('child.txt', 'w')
    fout.write('File created by child process %d' % pid)
else:
    fout = open('parent.txt', 'w')
    fout.write('File created by parent process')

fout.write('\nEnd of file')
```

## 等待子进程结束
fork()会创建一个子进程，该进程与父进程是相互独立的，也就是同时运行，大多数情况下父进程甚至会先于子进程结束，这样子进程就成了**孤儿**进程。在某些场景下，我们通常希望子进程结束后，父进程才继续运行，这个功能可以通过`waitpid`函数来实现。  
示例代码：
```
import os, time

def timeConsumingFunction():
    x = 1
    for n in xrange(10000000):
        x += 1

pid = os.fork()

if pid > 0:
    child = pid
else:
    timeConsumingFunction()
    os._exit(0)

t = time.time()
os.waitpid(child, 0)
print time.time() - t
```
其中，子进程中的`os._exit(0)`是必要的，否则子进程会返回到父进程中。
## 多个fork
有时我们需要fork()多个子进程，这样可以使用一个循环来实现。在这种场景下，`os._exit(0)`是必不可少的，它可以确保子进程不会继续执行循环，防止深层次的fork()。
示例代码:
```
import os, time

NUM_PROCESSES = 7

def timeConsumingFunction():
    x = 1
    for n in xrange(10000000):
        x += 1

children = []

start_time = time.time()
for process in range(NUM_PROCESSES):
    pid = os.fork()
    if pid:
        children.append(pid)
    else:
        timeConsumingFunction()
        os._exit(0)

for i, child in enumerate(children):
    os.waitpid(child, 0)

print time.time() - t
```
## 防止僵尸进程
为了防止子进程先于父进程退出而成为僵尸进程，需要采用某种机制来避免僵尸进程的产生：
* waitpid,父进程等待子进程结束后再退出
* fork两次
* 通过信号量的方式

发送信号一般有两种原因:
* 1(被动式)  内核检测到一个系统事件.例如子进程退出会像父进程发送SIGCHLD信号.键盘按下control+c会发送SIGINT信号

* 2(主动式)  通过系统调用kill来向指定进程发送信号

## 高级主题
参考：[孤儿进程与僵尸进程[总结]](http://www.cnblogs.com/anker/p/3271773.html)

## 参考
* [Fork und Prozesse](http://www.python-course.eu/forking.php)
* [Running multiple processes in Python](http://www.petercollingridge.co.uk/blog/running-multiple-processes-python)
* [Need to know how fork works?](http://stackoverflow.com/questions/6011635/need-to-know-how-fork-aworks)
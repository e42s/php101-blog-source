---
title: Python多线程
categories: F-Python
permalink: python-multithreading
translate_title: python-multithreading
date: 2015-11-02 12:13:15
tags:
description:
---
## 概述
Python提供了多线程编程的接口。Python提供了`threading`和`thread`两个模块用来实现多线程。threading模块封装了更底层的模块`thread`使得多线程编程更加容易，更pythonic。  
多线程编程提高了程序的并发能力，多线程共享同一个进程空间。


## Thread对象
使用线程最简单的方式就是使用目标函数初始化一个线程对象并调用`start()`函数:  

```
def worker():
    print "worker...."
    time.sleep(30)

threads = []
for i in range(5):
    thread = threading.Thread(target=worker)
    threads.append(thread)
    thread.start()
```

还可以在生成线程的时候，向线程传递参数：
```
import threading
import time


def worker(num):
    print "worker....%d" % num
    time.sleep(3)

threads = []
for i in range(500):
    thread = threading.Thread(target=worker, args=(i,))
    threads.append(thread)
    thread.start()
```

## 判断当前线程
在创建线程的时候，每个线程都有一个默认的名字，同时也可以通过传递参数为线程命名。为线程命名通过传递`name`参数来实现，获取线程名通过`threading.currentThread().getName()`来获得。代码示例如下:
```
import threading
import time


def worker(num):
    print threading.currentThread().getName()

threads = []
for i in range(500):
    name = "worker-%d" % i
    thread = threading.Thread(name=name, target=worker, args=(i,))
    threads.append(thread)
    thread.start()
```

除了上述方式，还可以通过定义`logging`模块的格式来获取当前线程名。

## 守护线程 VS 非守护线程
在上面的例子中，主程序会等待所有线程完成后再退出。有些程序会创建一个作为守护线程的进程，而不会阻塞主程序的退出。要使一个线程作为守护程序运行，调用线程对象的`setDaemon`方法即可。代码如下:
```
import threading
import time
import logging

logging.basicConfig(level=logging.DEBUG,
                    format='(%(threadName)-10s) %(message)s',
                    )

def daemon():
    logging.debug('Starting')
    time.sleep(2)
    logging.debug('Exiting')

d = threading.Thread(name='daemon', target=daemon)
d.setDaemon(True)

def non_daemon():
    logging.debug('Starting')
    logging.debug('Exiting')

t = threading.Thread(name='non-daemon', target=non_daemon)

d.start()
t.start()

d.join()
t.join()
```
默认的，主程序不会等待子线程的结束，如果需要等待子线程结束后主程序再退出，需要调用join()方法。


## 总结
本篇文章简单介绍了Python中多线程的入门知识，关于Python多线程还有许多知识，这会在以后的学习中慢慢补全。

## 参考
* [threading – Manage concurrent threads](https://pymotw.com/2/threading)
<br />
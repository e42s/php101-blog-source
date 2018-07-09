---
title: Python多线程基础
date: 2018-07-09 10:39:20
categories: F-Python
tags:
---

## 概述
使用多线程允许程序在同一个进程空间中并行的执行多个操作。这篇文章主要介绍Python的`threading`模块。需要注意的是，在Python中有一个`thread`模块，但是该模块在Python3中已经被重命名为`_thread`。实际上，threading模块就是基于`_thread`模块的封装，对外提供了多线程编程的接口。在平时的编程过程中，很少会直接涉及到`_thread`模块。

## 创建线程并传递参数
#### Thread对象
`Thread`类的定义在: [https://hg.python.org/cpython/file/3.4/Lib/threading.py](https://hg.python.org/cpython/file/3.4/Lib/threading.py)。

thread类的`__init__`方法定义如下:

```
def __init__(self, group=None, target=None, name=None,
             args=(), kwargs=None, *, daemon=None):
```
在这部分文章中，我们将用最简单的方法来使用线程。因此，如下代码所示，我们使用目标函数`f()`实例化thread类，并通过调用该类的`start()`方法让其开始工作。目标函数是一个被`run()`方法调用的可调用对象。默认为`None`,表示没有目标可被调用。

```
import threading

def f():
    print 'thread function'
    return

if __name__ == '__main__':
    for i in range(3):
        t = threading.Thread(target=f)
        t.start()
```
`start()`方法启动了线程活动。它在每一个线程对象中，至多被调用一次。如果被调用多次，将会抛出`RuntimeError`异常。

#### start()&run()方法
`run()`方法被`start()`方法调用，源码定义如下:

```
class Thread:
    ...

    def start(self):
        """Start the thread's activity.

        It must be called at most once per thread object. It arranges for the
        object's run() method to be invoked in a separate thread of control.

        This method will raise a RuntimeError if called more than once on the
        same thread object.

        """
        if not self._initialized:
            raise RuntimeError("thread.__init__() not called")

        if self._started.is_set():
            raise RuntimeError("threads can only be started once")
        with _active_limbo_lock:
            _limbo[self] = self
        try:
            _start_new_thread(self._bootstrap, ())
        except Exception:
            with _active_limbo_lock:
                del _limbo[self]
            raise
        self._started.wait()

    def _bootstrap(self):
        try:
            self._bootstrap_inner()
        except:
            if self._daemonic and _sys is None:
                return
            raise

    def _bootstrap_inner(self):
        try:
         ...

            try:
                self.run()
            except SystemExit:
                pass
            except:
```

#### 传递参数
为了使线程看起来更有用，我们传递参数给线程执行函数。下面的代码传递一个整型参数到目标函数，并打印他。`args`参数是一个参数元组，用来传递给目标函数。

```
def f(i):
    print(f'Thread Function....{i}')
    return


if __name__ == '__main__':
    for i in range(5):
        thread = threading.Thread(target=f, args=(i,))
        thread.start()
    print('==========')
```

## 识别线程: 名字和日志
#### 识别线程
尽管每一个线程实例都有一个默认的名字，我们仍然可以在创建线程的时候，为其设置名字。命名线程在服务器处理多个不同的线程服务时，是非常有用的。

```
def f1():
    print(f'{threading.current_thread().getName()}-Starting......')
    time.sleep(1)
    print(f'{threading.current_thread().getName()}-Exit......')


def f2():
    print(f'{threading.current_thread().getName()}-Starting......')
    time.sleep(2)
    print(f'{threading.current_thread().getName()}-Exit......')


def f3():
    print(f'{threading.current_thread().getName()}-Starting......')
    time.sleep(3)
    print(f'{threading.current_thread().getName()}-Exit......')


if __name__ == '__main__':
    t1 = threading.Thread(target=f1)
    t2 = threading.Thread(target=f2, name='f2')
    t3 = threading.Thread(target=f3, name='f3')


    t1.start()
    t2.start()
    t3.start()

    print ('=================')
```
从运行结果可以看出，`t2`和`t3`都有自定义的名字，尔`t1`使用的是默认线程名。

#### logging模块
通过打印线程名称我们可以识别运行的每个线程。但是我们真正需要的是在日志模块通过`%(threadName)s`格式来记录线程名。在日志信息中包含线程名，使得追踪问题变得更简单。需要注意的是，日志模块是线程安的，因此来自不同线程的输出信息都会保持独立。

```
logging.basicConfig(level=logging.DEBUG, format='[%(levelname)s] (%(threadName)-9s) %(message)s',)


def f1():
    logging.debug('Start......')
    time.sleep(1)
    logging.debug('Exiting......')


def f2():
    logging.debug('Start......')
    time.sleep(2)
    logging.debug('Exiting......')


def f3():
    logging.debug('Start......')
    time.sleep(3)
    logging.debug('Exiting......')


if __name__ == '__main__':
    t1 = threading.Thread(target=f1)
    t2 = threading.Thread(target=f2, name='f2')
    t3 = threading.Thread(target=f3, name='f3')

    t1.start()
    t2.start()
    t3.start()

    print ('=================')
```

## 守护线程
有些线程会做一些后台任务，如发送心跳包，定期执行垃圾回收，或者其他一些事情。这些仅仅在主程序运行的时候有用，而且应该在其他非守护线程结束的时候立即结束。  
如果没有守护线程，你不得不一直跟踪他们，在程序完全退出的时候，告诉他们应该退出。但是如果设置了守护线程，就可以忽略掉这些守护线程，主程序退出的时候，守护线程也会自动退出。
要将线程设置为一个守护线程，通过`setDaemon()`方法即可。

```
if __name__ == '__main__':
    t1 = threading.Thread(target=f1)
    t2 = threading.Thread(target=f2, name='f2')
    t3 = threading.Thread(target=f3, name='f3')

    t3.setDaemon(True)

    t1.start()
    t2.start()
    t3.start()
``` 

输出结果如下:

```
=================
[DEBUG] (Thread-1 ) Start......
[DEBUG] (f2       ) Start......
[DEBUG] (f3       ) Start......
[DEBUG] (Thread-1 ) Exiting......
[DEBUG] (f2       ) Exiting......

```
由结果可以看出，t3为守护线程，主程序退出后，守护线程并没有执行完，而是在程序退出后直接退出。

## join()方法
如果要等待一个线程完全执行完以后再执行主程序，可以使用`join()`方法。

默认的，`join()`将无限的阻塞程序运行。在下面的例子中，join()阻塞了程序，直到调用阻塞的线程结束。

```
if __name__ == '__main__':
    t1 = threading.Thread(target=f1)
    t2 = threading.Thread(target=f2, name='f2')
    t3 = threading.Thread(target=f3, name='f3')

    t3.setDaemon(True)

    t1.start()
    t2.start()
    t3.start()

    t1.join()
    t2.join()
    t3.join()

    print ('=================')
```

同时,join()也支持一个默认参数，如`join(5）`表示阻塞程序5秒。

## thread子类
到目前为止，我们使用线程的方式都是通过实例化Thread类。有时，我们想让我们自己的类以线程的方式工作。因此，我们需要让我们的类继承Thread类。

继承Thread类时，大概形式如下:

```
class MyThread(threading.Thread):
    pass
```

当一个线程启动时，它会做一些初始化工作，然后调用`run()`方法，run()方法调用传递给构造函数的目标函数。Thread类代表一个独立运行的控制线程。有两种方法指定那个活动:

* 传递给构造函数一个可调用对象
* 在子类中覆盖run方法

在这部分，我们将创建Thread类的子类，并且覆盖run()方法:

```
class MyThread(threading.Thread):

    def run(self):
        pass


if __name__ == '__main__':
    for i in range(3):
        t = MyThread()
        t.start()
```
一旦线程对象被创建，必须通过调用`start()`方法来启动他的活动。该方法在一个独立的线程控制中调用`run()`方法。

#### 向自定义线程中传递参数
使用方法如下:

```
class MyThread(threading.Thread):

    def __init__(self, group=None, target=None, name=None,
                 args=(), kwargs=None, verbose=None):
	super(MyThread,self).__init__(group=group, target=target, 
			              name=name, verbose=verbose)
	self.args = args
	self.kwargs = kwargs
	return

    def run(self):
	logging.debug('running with %s and %s', self.args, self.kwargs)
	return

if __name__ == '__main__':
    for i in range(3):
	t = MyThread(args=(i,), kwargs={'a':1, 'b':2})
	t.start()
```

## 总结
关于线程，还有很多其他方面的知识，如`Timer`, `Event`, `Lock`等，这些知识等以后深入。

## 参考
[Python 201: A Tutorial on Threads](https://www.blog.pythonlibrary.org/2016/07/28/python-201-a-tutorial-on-threads/)
[Daemon Threads Explanation](https://stackoverflow.com/questions/190010/daemon-threads-explanation)
[MULTITHREADING - CREATING THREADS](http://www.bogotobogo.com/python/Multithread/python_multithreading_creating_threads.php)
<br />



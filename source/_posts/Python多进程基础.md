---
title: Python多进程基础
categories: F-Python
translate_title: python-multi-process-base
date: 2018-07-06 18:41:38
tags:
---


## 概述
之前写了篇[文章](http://www.php101.cn/2015/08/21/python-multi-process-(fork).html)想深入了解下多进程、多线程等技术细节，但是无奈自己操作系统等方面的知识太薄弱，所以写了个大概，现在也忘得差不多了。
同时，自己平时的工作中，遇到并发的场景不多，所以对并发方面的技术一直是一知半解，这篇文章从基础层面总结下Python多进程相关知识。

Python的`multiprocessing`有很多强大的特性。本篇文章会通过很多简单的例子，来展示如何使用该模块在Python中进行并行编程。


## Process类
使用多进程最基本的方法就是通过`multiprocessing`模块中的`Process`类来创建多进程。多进程通过创建一个`Process`对象并且调用该对象的`start`方法来进行工作。下面是一个简单示例:

```
def square(n):
    rand_int = random.randint(3, 5)
    time.sleep(rand_int)
    print(f'Sleep {rand_int} Seconds......')
    result = n**2
    print(f'{n} squared is {result}')
    return result


if __name__ == '__main__':
    start_time = int(time.time())
    print(f'Start Time....{start_time}')
    process = mp.Process(target=square, args=(3, ))
    process.start()
    process.join()

    end_time = int(time.time())
    print(f'End Time......{end_time}')
    print(f'Execute Time......{end_time - start_time}')
```

运行程序，程序输出结果如下:

```
Start Time....1530875904
Sleep 5 Seconds......
3 squared is 9
End Time......1530875909
Execute Time......5
```
观察代码在本地的运行结果，程序的运行时间为5秒。这个例子并不能说明多进程的优点，因为程序的运行时间和不使用多进程的时间几乎一样。将代码修改为如下示例:

```
def square(n):
    rand_int = random.randint(3, 5)
    time.sleep(rand_int)
    print(f'Sleep {rand_int} Seconds......')
    result = n**2
    print(f'{n} squared is {result}')
    return result


if __name__ == '__main__':
    start_time = int(time.time())
    print(f'Start Time....{start_time}')
    process = mp.Process(target=square, args=(3, ))
    process1 = mp.Process(target=square, args=(3, ))
    process2 = mp.Process(target=square, args=(3, ))
    process3 = mp.Process(target=square, args=(3, ))

    process.start()
    process1.start()
    process2.start()
    process3.start()


    process.join()
    process1.join()
    process2.join()
    process3.join()

    end_time = int(time.time())
    print(f'End Time......{end_time}')
    print(f'Execute Time......{end_time - start_time}')
```

运行程序，输出结果如下:

```
Start Time....1530876061
Sleep 3 Seconds......
3 squared is 9
Sleep 3 Seconds......
3 squared is 9
Sleep 3 Seconds......
3 squared is 9
Sleep 5 Seconds......
3 squared is 9
End Time......1530876066
Execute Time......5
```
从输出结果可以看出，程序的运行结果依然是5秒，如果不使用多进程，那么程序的运行结果应该为`square`函数执行多次的时间总和。

#### join()解释
在之前的文章中提到了在使用多进程的时候，如果对子进程处理不当，可能会出现`孤儿进程`或者`僵尸进程`。  

`join()`函数的作用是父进程等待子进程结束并返回以后再继续执行，如果不调用`join()`方法等待子进程返回，那么父进程中在子进程之后的代码就会立即执行。  

如果不调用`join()`方法，输出结果如下:

```
Start Time....1530876335
End Time......1530876335
Execute Time......0
Sleep 3 Seconds......
Sleep 3 Seconds......
3 squared is 9
3 squared is 9
Sleep 4 Seconds......
3 squared is 9
Sleep 5 Seconds......
3 squared is 9
```

stackoverflow上有个关于对`join()`方法的解释, [问题链接](https://stackoverflow.com/questions/25391025/what-exactly-is-python-multiprocessing-modules-join-method-doing)。

#### 使用生成器创建多进程
上面的代码通过手工创建了4个进程，代码比较冗余，可以通过Python生成器来简化上述代码, 修改后的代码如下:

```
if __name__ == '__main__':
    start_time = int(time.time())
    print(f'Start Time....{start_time}')

    process_list = [mp.Process(target=square, args=(n, )) for n in range(5)]

    for process in process_list:
        process.start()

    for process in process_list:
        process.join()

    end_time = int(time.time())
    print(f'End Time......{end_time}')
    print(f'Execute Time......{end_time - start_time}')
```

## Pool类
`multiprocessing`提供了一个更方面的方法来创建多进程，那就是`进程池`，通过`Pool`类可以创建一个进程池。  

要创建一个进程池，首先需要实例化一个Pool类，代码如下:

```
pool = mp.Pool(processes=4)
```

> 其中，参数`processes`参数表示进程池中进程的数量，该数量最好等于运行环境的CPU核数。

Pool类中有四个主要的方法:

* apply
* apply_async
* map
* map_async

`Pool.apply` 和 `Pool.map`与Python内建的apply和`map`方法一样。下面先通过一些简单的示例来演示这四个方法的用法，然后再做一些深入的对比。

#### apply方法
apply方法的使用方法如下:

```
start_time = int(time.time())
print(f'Start Time....{start_time}')

pool = mp.Pool(processes=4)
pool.apply(square, args=(5,))

end_time = int(time.time())
print(f'End Time......{end_time}')
print(f'Execute Time......{end_time - start_time}')
```




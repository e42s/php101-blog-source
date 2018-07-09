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

pool = mp.Pool()
result = [pool.apply(square, args=(n,)) for n in range(5)]
print(result)


end_time = int(time.time())
print(f'End Time......{end_time}')
print(f'Execute Time......{end_time - start_time}')
```
apply方法会放回target函数的运行结果。

#### apply_async方法
apply_async方法与apply方法类似，不同的是apply_async方法不用等待所有的进程执行结束并返回执行结果而是直接返回。换句话说，apply_async是异步的，使用方法如下:

```
if __name__ == '__main__':
    start_time = int(time.time())
    print(f'Start Time....{start_time}')

    pool = mp.Pool()
    result_apply = [pool.apply_async(square, args=(n,)) for n in range(5)]
    print(result_apply)
    # for result in result_list:
    #     print(result.get())

    end_time = int(time.time())
    print(f'End Time......{end_time}')
    print(f'Execute Time......{end_time - start_time}')
```
输出结果如下:

```
Start Time....1531029878
[<multiprocessing.pool.ApplyResult object at 0x103607908>, <multiprocessing.pool.ApplyResult object at 0x1036079e8>, <multiprocessing.pool.ApplyResult object at 0x103607a90>, <multiprocessing.pool.ApplyResult object at 0x103607b38>, <multiprocessing.pool.ApplyResult object at 0x103607c18>]
End Time......1531029878
Execute Time......0
```
apply_async函数返回`ApplyResult`, 要想获取函数的执行结果可以使用`get()`方法，但是`get()`方法会像`apply`方法一样阻塞程序运行，等待所有进程结束并返回结果以后再继续执行。

相对于`apply`方法来说，`apply_async`方法是异步的，所以`apply_async`方法可以设置回调函数，回调函数会在进程调用的函数执行结束后自动执行。使用方法如下:

```
pool = mp.Pool()
result_apply = [pool.apply_async(square, args=(n,), callback=callback) for n in range(5)]
print(result_apply)
pool.close()
pool.join()
```

### map方法
`map`方法同Python内建的方法一样，使用方法如下:

```
if __name__ == '__main__':
    start_time = int(time.time())
    print(f'Start Time....{start_time}')

    num_list = [2, 4, 6, 8, 10]
    pool = mp.Pool()
    result = pool.map(square, num_list)
    print(result)

    end_time = int(time.time())
    print(f'End Time......{end_time}')
    print(f'Execute Time......{end_time - start_time}')
```
输出结果如如下:

```
Start Time....1531030555
Sleep 3 Seconds......
4 squared is 16
Sleep 4 Seconds......
6 squared is 36
Sleep 5 Seconds......
2 squared is 4
Sleep 5 Seconds......
8 squared is 64
Sleep 5 Seconds......
10 squared is 100
[4, 16, 36, 64, 100]
End Time......1531030561
Execute Time......6
```

从结果可以看出，不同于`apply`方法，`map`方法一次启动多个进程，因此调用函数的执行是无序的。

#### map_async方法
同上可知，map_async方法的使用方法

#### 总结
* apply: apply方法，一次只能放入一组参数，返回一个结果
* apply_async: 该方法同样一次只能放入一组参数，返回一个结果，可以传入回调函数
* map: 该方法放入迭代参数，并返回多个结果
* map_async: 该方法放入多个参数，并返回多个结果，可以传入回调函数

> `apply*`方法一次启动一个进程，`map`方法一次启动多个进程。

## Lock锁
锁机制保证了一次只有一个进程运行，避免了资源争抢。使用方法如下:

```
lock = mp.Lock()
process_list = [mp.Process(target=square, args=(n, lock)) for n in range(4)]
for process in process_list:
    process.start()
for process in process_list:
    process.join()

```

## 总结
这篇文章简单介绍下Python多进程的基础知识，关于并发要考虑的问题还很多，等实际项目中再深入了解。

## 参考
[Python 201: A multiprocessing tutorial](https://www.blog.pythonlibrary.org/2016/08/02/python-201-a-multiprocessing-tutorial/)
[multiprocessing.Pool: When to use apply, apply_async or map?](https://stackoverflow.com/questions/8533318/multiprocessing-pool-when-to-use-apply-apply-async-or-map)
[python multiprocessing and threads 01](http://songhuiming.github.io/pages/2016/06/18/python-multiprocessing-and-threads-01/)
[Introduction to Multiprocessing in Python](https://code.tutsplus.com/tutorials/introduction-to-multiprocessing-in-python--cms-30281)


<br />



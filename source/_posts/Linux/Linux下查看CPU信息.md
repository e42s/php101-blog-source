---
title: Linux下查看CPU信息
categories: D-Linux
permalink: view-cpu-information-under-linux
translate_title: view-cpu-information-under-linux
date: 2015-05-05 13:59:34
tags:
description:
---
## 概述
Nginx配置中，配置项`worker_processes`用来设置Nginx的进程数，该项配置的值，建议被设置为CPU的总核心数。这篇文章简单介绍了Linux下如何CPU信息。

## nproc
npoc命令显示当前CPU的核心数。
```
┌─[tudouya@mac-ubuntu] - [~] - [2015-05-05 02:07:07]
└─[0] <> nproc
4
```

## lscpu
命令`lscpu`从`/proc/cpuinfon`文件中收集了CPU相关的信息，并以友好可读的方式显示结果：
```
┌─[tudouya@mac-ubuntu] - [~] - [2015-05-05 02:07:09]
└─[0] <> lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                4
On-line CPU(s) list:   0-3
Thread(s) per core:    1
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 70
Model name:            Intel(R) Core(TM) i7-4850HQ CPU @ 2.30GHz
Stepping:              1
CPU MHz:               2294.000
BogoMIPS:              4588.00
Hypervisor vendor:     KVM
Virtualization type:   full
L1d cache:             32K
L1i cache:             32K
L2 cache:              256K
L3 cache:              6144K
L4 cache:              131072K
NUMA node0 CPU(s):     0-3
```

## /proc/cpuinfo
文件`/proc/cpuinfo `显示了详细的CPU信息，可以使用`cat`或`less`命令查看该文件。

在文件内容中：
* physical id：标识有几个CPU
* cpu cores：表示每个CPU有几个核心

## 总结
了解Linux下CPU的信息。
<br />
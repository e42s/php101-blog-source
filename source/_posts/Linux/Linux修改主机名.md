---
title: Linux修改主机名
categories: D-Linux
permalink: linux-modify-host-name
translate_title: linux-modifies-host-name
date: 2015-01-18 03:42:06
tags:
description:
---
## 概述
最近一直在使用阿里云，购买了阿里云后，默认的有一个主机名，但是主机名看起来怪怪的，就想把主机名修改下，记录下来。

## 查看主机名
Linux下查看主机名有两种方式：  
1.打开一个终端窗口，在命令提示符中可以看到主机名，主机名通常位于"@"符号之后。  
2.使用命令，`hostname`或`uname -n`,均可以查看当前主机的主机名。

## 临时修改主机名
在终端窗口中执行命令“hostname 新主机名”,即可临时修改主机名。如
```
hostname chain # 新的主机名
```
这样主机名字就临时被修改为`chain`，但是终端下不会立即显示生效后的主机名，重开一个终端窗口(通过ssh连接的终端需要重新连接才可以);

## 永久修改主机名
### Centos
在Centost系统中，主机名存放在`/etc/sysconfig/network`文件中，network文件内容如下:
```
NETWORKING=yes                                    
HOSTNAME=主机名                                     
NETWORKING_IPV6=no                                
PEERNTP=no                                        
GATEWAY=
```
修改`HOSTNAME`的值为自己需要用的主机名，重启系统，新的主机名就可以生效了。

### Ubuntu
在Ubuntu系统中，主机名存放在/etc/hostname文件中，同样修改hostname的值为自己想要的主机名即可。

## 总结
最近工作有点乱，博客更新不及时，这几天感觉算是稳定下来了。   
让那些蛋疼的人都随风而去吧。~~  
纪念下吧~~：)。
<br />

## 附加(2015-11-05)
今天读到了一篇文章也是关于修改Linux主机名的，本文修改Linux主机名的方式并没有错误，但是该文章对修改主机名的机制做了更深层次的探讨。  
文章地址:[深入理解Linux修改hostname](http://www.cnblogs.com/kerrycode/p/3595724.html)

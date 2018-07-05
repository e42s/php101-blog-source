---
title: ssh连接远程主机慢问题
categories: D-Linux
permalink: sh-slow-conection-to-remote-host
translate_title: ssh-connection-to-remote-host-slow-problem
date: 2015-07-22 02:02:51
tags:
description:
---
## 概述
ssh是经常会用到的服务，之前有篇文章介绍了ssh相关的知识，文章地址[VPS添加SSH Key免密码登录](http://www.php101.cn/2014/10/12/VPS%E6%B7%BB%E5%8A%A0SSH-Key%E5%85%8D%E5%AF%86%E7%A0%81%E7%99%BB%E5%BD%95/)。  

最近遇到的问题是，使用ssh连接远程主机时，会出现特别慢的现象，哪怕是连接虚拟机也会出现这种情况，查了一些资料，总算解决了这些问题。


## 原因1:DNS解析问题
连接慢的主要原因是DNS解析导致，需要修改远程主机的ssh服务配置，在配置文件`/etc/ssh/sshd_config`修改如下配置:
```
UseDNS no
# GSSAPI options
GSSAPIAuthentication no
```
更改完成后，重启ssh服务即可。

## 原因2:host文件
如果修改了远程主机的配置文件后，仍然连接很慢，需要检查ssh服务端上/etc/hosts文件中，127.0.0.1对应的主机名是否和 uname -n的结果一样，或者把本机ip和hostname(uname -n结果)加入到/etc/hosts里。

## 调试
除了上述的两种解决方法，还可以使用`ssh -v`命令，调试连接慢的原因。


## 总结
(⊙o⊙)…，进步不要多，每天一点点，奋斗吧(*^__^*) ……
<br />
---
title: SSH端口转发连接禁止远程连接的MySQL服务器
categories: D-Linux
permalink: sh-port-forwarding-conects-mysql-servers-that-prohibit-remote-conections
translate_title: ssh-port-forwarding-connection-prohibits-remote-connection-of-mysql-server.
date: 2016-10-10 20:41:38
tags:
description:
---
## 概述
在服务器上部署MySQL服务器的时候，出于安全考虑，一般会建议设置MySQL服务禁止用户远程连接。但是，禁止远程连接，本地就不能通过图形化的界面操作MySQL，这在开发的时候🈶特别不方便，本人以前也一直是允许远程连接，虽然觉得可能存在安全隐患，但是为了开发方便，只能尽可能的把密码设置的复杂点。   

前一段时间，阅读了一篇关于SSH使用的文章，里边介绍了如何使用**本地端口转发**来连接禁止远程连接的MySQL服务，今天抽空整理了一下，发现确实好用，记录下来。  


## 使用方法
在使用该方法的时候，为了理解，大家可能需要掌握以下两个知识: **SSH端口转发原理**和**SSH配置文件**。   

好了，言归正传，使用SSH端口转发连接MySQL其实只需要一个配置，然后运行一个命令即可。  

配置文件如下:  

```
Host tudouya-mysql
    Hostname www.test.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/vps/tdy_rsa
    LocalForward 9906 127.0.0.1:3306
    User root
    Port 22
```

然后运行如下命令:  

```
ssh -f -N tudouya-mysql
```

## 遇到的问题
刚开始的时候，按照上边的方法配置，并不能连接到MySQL，试了一下几个方法：

1.修改SSH配置文件，允许转发  

```
#AllowAgentForwarding yes
#AllowTcpForwarding yes
X11Forwarding no
```
将这几个配置项都开启并设置为**yes**，并没有解决问题。

2.修改mysql配置文件  

```
bind-address = 127.0.0.1
```
这个是参考了SO上的一个问题，同样没有解决问题，当然，这一步配置是必须得，因为我们要禁止远程连接。

试过了以上两个方法，并且各种组合着试都没解决问题，最后才发现自己的MySQL监听端口并不是**3306**，咕~~(╯﹏╰)b，找到了问题的根源并解决了。  



## 总结
自己配置的服务器又消除了一个安全隐患，O(∩_∩)O哈哈~。

## 参考
* [SSH 简化配置](http://gold.xitu.io/entry/5704cf8e71cfe4005dc76f18)
* [ssh tunnel refusing connections with “channel 2: open failed"](http://serverfault.com/questions/489192/ssh-tunnel-refusing-connections-with-channel-2-open-failed) 


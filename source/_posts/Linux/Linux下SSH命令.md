---
title: Linux下SSH命令
categories: D-Linux
tags:
  - SSH
permalink: sh-comand-under-linux
translate_title: the-ssh-command-under-linux
date: 2014-09-26 19:00:31
---
## 概述
SSH是Linux下常用的远程登录工具，哪怕是我们不经常用这个命令，但是最起码也听说过这个命令，下面是对SSH常用命令的总结。
## 登录
最简单的 SSH 命令只需要指定用户名和主机名参数即可. 主机名可以是 IP 地址或者域名. 命令格式如下:
```
ssh user@hostname
```
SSH默认连接到目标主机的22端口上,但是由于各种原因你可能需要连接到其他端口:
```
ssh -p 10092 user@hostname
```
<br />
## 退出
使用SSH登录远程主机执行完任务以后，就可以退出远程主机，使用命令如下：
```
exit
```
## 挂载远程文件系统
有一个很赞的基于SSH的工具叫sshfs. sshfs可以在本地直接挂载远程主机的文件系统.要使用这个功能需要在客户机上安装sshfs。[sshfs下载地址](http://fuse.sourceforge.net/sshfs.html)。命令格式如下：
```
sshfs -o idmap=user user@hostname:/home/user ~/Remote
```
比如下面这条命令:
```
sshfs -o idmap=user pi@10.42.0.47:/home/pi ~/Pi
```
该命令就将远程主机 pi 用户的主目录挂载到本地主目录下的 Pi 文件夹.    
要详细了解可以参考[sshfs](http://linoxide.com/how-tos/sshfs-mount-remote-directories/)教程。
<br />
## X11图形界面
假如现在你想要在远程主机运行一个图形界面的程序, SSH 已经帮你想到了! 用前面提到的 SSH 基本命令加上参数 -X 连接到远程主机即可开启 X11 转发功能. 登录后你可能觉得没什么差别, 但是当你运行一个图形界面程序后就会发现其中的不同的.
```
ssh -X root@192.168.1.199
```
## 转义字符
SSH提供了多样的转义字符功能. 用 SSH 连接到任意一台远程主机然后输入 ~? 你就可以看到支持的转义字符和功能说明列表：
```
[root@tj ~]# ~?

Supported escape sequences:
 ~.   - terminate connection (and any multiplexed sessions)
 ~B   - send a BREAK to the remote system
 ~C   - open a command line
 ~R   - request rekey
 ~V/v - decrease/increase verbosity (LogLevel)
 ~^Z  - suspend ssh
 ~#   - list forwarded connections
 ~&   - background ssh (when waiting for connections to terminate)
 ~?   - this message
 ~~   - send the escape character by typing it twice
(Note that escapes are only recognized immediately after newline.)
```
<br />
## 构建SSH密钥对
### 创建密钥对
运行以下命令创建密钥对:
```
ssh-keygen -t dsa
```
此命令会要求你输入密码(可以留空), 然后就会生成密钥并会显示一张该密钥对应的随机图.
### 寻找主机密钥
在你准备添加密钥之前不妨先用以下命令看看是否已经添加了对应主机的密钥了:
```
ssh-keygen -F 10.42.0.47
```
### 删除主机密钥
某些情况下, 比如主机地址更改或者不再使用某个密钥, 你就可能需要删除某个密钥,用以上命令就可删除. 这比手动在 ~/.ssh/known_hosts 文件中删除要方便很多:
```
ssh-keygen -R 10.42.0.47
```
<br />
## 配置SSH
如果你需要改变 SSH 的配置, 请用你喜好的文本编辑器打开 /etc/ssh/sshd_config 进行编辑。在配置文件 /etc/ssh/sshd_config 中你还可以找到端口号, 空闲超时时间等配置项. 配置项大都比较容易理解, 但是保险起见在你修改一些不是很确定的配置项时最好参考下 SSH 的帮助文档.
<br />
## 一些好用的SSH命令
### 复制SSH密钥到目标主机，开启无密码SSH登录
```
ssh-copy-id user@host
```
### 从某主机的80端口开启到本地主机2001端口的隧道
```
ssh -N -L2001:localhost:80 某主机
```
### 比较远程和本地文件
```
ssh 用户名@远程主机 cat /path/to/remotefile | diff /path/to/localfile –
```
### 通过中间主机建立SSH连接
```
ssh -t 中间主机 ssh 远程不可直接访问的主机
```
从本地网络无法直接访问的主机，但可以从中间主机所在网络访问时，这个命令通过到中间主机的“隐藏”连接，创建连接到远程不可直接访问的主机的连接。
### 创建到目标主机的持久化连接
```
ssh -MNf 用户名@主机
```
在后台创建到目标主机的持久化连接，将这个命令和你~/.ssh/config中的配置结合使用：
```
Host host
ControlPath ~/.ssh/master-%r@%h:%p
ControlMaster no
```
所有到目标主机的SSH连接都将使用持久化SSH套接字，如果你使用SSH定期同步文件（使用rsync/sftp/cvs/svn），这个命令将非常有用，因为每次打开一个SSH连接时不会创建新的套接字。
### 通过SSH连接屏幕
```
ssh -t remote_host screen –r
```
直接连接到远程屏幕会话（节省了无用的父bash进程）。
### 端口检测（敲门）
```
knock 主机 3000 4000 5000 && ssh -p 端口 用户名@主机 && knock 主机 5000 4000 3000
```
在一个端口上敲一下打开某个服务的端口（如SSH），再敲一下关闭该端口，需要先安装knockd，下面是一个配置文件示例:
```
[options]
logfile = /var/log/knockd.log
[openSSH]
sequence = 3000,4000,5000
seq_timeout = 5
command = /sbin/iptables -A INPUT -i eth0 -s %IP% -p tcp –dport 22 -j ACCEPT
tcpflags = syn
[closeSSH]
sequence = 5000,4000,3000
seq_timeout = 5
command = /sbin/iptables -D INPUT -i eth0 -s %IP% -p tcp –dport 22 -j ACCEPT
tcpflags = syn
```
### 从已知主机列表中删除一个主机
```
ssh-keygen -R 要删除的主机名
```
### 通过SSH将MySQL数据库复制到新服务器
```
mysqldump –add-drop-table –extended-insert \
  –force –log-error=error.log \
  -uUSER -pPASS OLD_DB_NAME \
  | ssh -C user@newhost “mysql -uUSER -pPASS NEW_DB_NAME”
```
通过压缩的SSH隧道Dump一个MySQL数据库，将其作为输入传递给mysql命令，我认为这是迁移数据库到新服务器最快最好的方法。
### 从一台没有ssh-copy-id命令的主机将你的SSH公钥复制到服务器
```
cat ~/.ssh/id_rsa.pub | ssh user@machine “mkdir ~/.ssh; cat >> ~/.ssh/authorized_keys”
```
如果你使用Mac OS X或其它没有ssh-copy-id命令的*nix变种，这个命令可以将你的公钥复制到远程主机，因此你照样可以实现无密码SSH登录。
<br />
## 引用
1.[Linux 下 SSH 命令实例指南 ](http://linux.cn/article-3858-1.html?fromuid=14763)  
2.[牢记25个最佳的SSH命令](http://linux.cn/article-1206-1.html)  

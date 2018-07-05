---
title: LNMP服务器安全加固
categories: D-Linux
permalink: lnmp-server-security-consolidation
translate_title: safety-and-reinforcement-of-lnmp-server
date: 2016-09-25 22:03:39
tags:
description:
---

## 概述
现如今，安全越来越引起人们的重视了。安全隐患存在于各个方面，对于安全来说，基本上就是**木桶原理**。本篇文章，主要是针对一些常见的容易引起安全问题的点进行安全加固，尽量减少安全问题的发生。 


## SSH篇
### Port
系统缺省使用**22**号端口，将监听端口更改为其他数值（最好是1024以上的高端口，以免和其他常规服务端口冲突），这样可以增加入侵者探测系统是否运行了sshd守护进程的难度。  

```
Port 11111
```
<br />

### ListenAddress
对于在服务器上安装了多个网卡或配置多个IP地址的情况，设定sshd只在其中一个指定的接口地址监听，这样可以减少sshd的入口，降低入侵的可能性。   

```
ListenAddress 192.168.0.1 
```
<br />

### Protocol
禁止使用版本1协议，因为其存在设计缺陷，很容易使密码被黑掉。  

```
Protocol 2
```
<br />

###  X11Forwarding
关闭X11Forwarding，防止会话被劫持。  

```
X11Forwarding no
```

## Nginx篇
### 禁止某些文件类型的访问
某些文件不小心传如web目录后存在很大风险。  

```
location *.(txt|doc|docx|rar)$ {
	root /var/www/www.xxx.xxx;
	Deny all;
}
```
<br />

### 禁止访问某目录
一些敏感目录禁止直接访问。  

```
location ^/(web-inf)/{ 
	Deny all; 
}
```
<br />

### 禁止某个IP或网段访问
限制某些恶意用户的IP或网段。  

```
location /{
	Deny xxx.xxx.xxx.xxx;
	Allow xxx.xxx.xxx.0/24;
	Deny all;
}
```
<br />

### 修改Nginx名称和版本号
防止恶意者收集服务器信息.  

```
vim src/http/ngx_http_header_fileter_module.c
Staticchar ngx_http_server_string[]=”Server: tudouya”;
Staticchar ngx_http_server_full_string[]=’Server: tudouya’
```
<br />

### 单用户并发链接限制
限制单个用户IP不能同时请求超过50个链接.

```
limit_conn slimits 50;
```
<br />

### 限制可用方法
如果应用只用到GET/POST/HEAD，则禁止其他方法.

```
if($request_method !^(GET|HEAD| POST)$){
	return 444;
}
```
<br />

### 阻止用户代理
阻止用户代理访问、扫描器、机器人、垃圾邮件等.  

```
暂无
```
<br />

### 禁止其他网站引用本站图片
如果其他网站应用本站图片将消耗本站资源。  

```
location /images/{
	valid_reerers none blocked www.xxx.com xxx.com;
	if($invalid_referer){
		return 403;
	}
}
# 替换图片
if($invalid_referer){
	rewrite ^/images/uploads.*.(gif|jpg|jpeg|png|bmp)$ http://www.xxx.xxx/noimp.png last
}
```



## MySQL篇
### 增强root帐户密码登陆、删除空密码
原因：  

* 简单密码容易暴力破解。
* mysql默认是空密码。

解决： 

* 增强密码强度, 22位以上, 同时包含大写字母、小写字母、数字、特殊字符, 密码不重复使用
<br />

### 删除默认数据和帐户
Mysql默认会有空用户和test库  

```
删除test库和除root外帐户再按照业务需求添加
mysql> drop database test;
mysql> delete from user where not (user=’root’);
```
<br />

### 更改root帐户名称
Mysql管理员帐号默认名称为root，存在被爆破的风险.  

```
更改root帐户名称以增大爆破成本
mysql> update user set user=’newrootname’ where user=’root’;
mysql> flush privileges;
```
<br />

### 限制用户的连接
同个用户可以多个远程链接，会导致性能下降.  

```
# 修改my.cnf中max_user_connections
vim /etc/my.cnf 
max_user_connections 150
```
<br />

### 目录权限限制
mysql默认安装在/usr/local/mysql下，数据库文件在/usr/local/mysql/var下，权限不正确会导致数据存在被COPY走的风险.  

```
# 修改目录所有者 
chown -R root /usr/local/mysql
chown -R mysql.mysql /usr/local/mysql/var/
```
<br />

### 历史命令泄漏
linux的历史命令可能会泄漏mysql的帐号密码等信息.  

```
# 限制历史命令记录为一个较小的数 
vim ~/.bash_profile 
HISTSIZE=3 
HISTFLESIZE=3
# 限制不记录某些命令 
export HISTCONTROL=ignorespace # 你在执行任何命令前只需要加一个空格就不会记录 
# 定时清除历史命令 
history -c
```
<br />

### 限制访问数据的IP
不要使用%来设置来源IP，指定精确的来源IP限制.  

```
grant selete on database.table to ‘app’@’xx.xx.xx.xx’ identified by ‘password’ with grant option;
flush privileges;
```
<br />

### 为应用创建单独的用户
```
GRANT ALL PRIVILEGES  ON dbname.*  TO 'dbuser'@'%' IDENTIFIED BY '123456'  WITH GRANT OPTION;
```

## 参考
* [网络安全：提高sshd服务安全 降低网络入侵的风险](http://www.kuqin.com/security/20100322/82054.html#source)
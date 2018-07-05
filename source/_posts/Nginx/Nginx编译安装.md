---
title: Nginx编译安装
categories: G-WebServer
permalink: nginx-compile-and-instal
translate_title: nginx-compilation-and-installation
date: 2015-08-06 10:57:01
tags:
description:
---
## 概述
折腾一下Nginx的源码方式安装，后续会试验下Redis、Mongo等。~~人生苦短，快来折腾吧~~

## 依赖包
与使用Linux包管理器(Yum, apt)不同，Linux包管理器会自动安装Nginx所需要的依赖包。编译安装Nginx的时候就需要自动安装依赖包了。
### PCRE
Nginx的Rewrite模块和HTTP核心模块都依赖**Perl Compatible Regular Expression (PCRE)**。  
安装方式:
```
# centos
yum install pcre pcre-devel
# ubuntu
apt-get install libpcre3 libpcre3-dev
```
<br />
### zlib
Nginx的gzip压缩会使用到zlib库。  
安装方式:
```
#centos
yum install zlib zlib-devel
#ubuntu
apt-get install zlib1g zlib1g-dev
```
<br />
### openssl
Nginx使用openssl提供对HTTPS的支持。  
安装方式:
```
#centos 
yum install openssl openssl-devel#ubuntu
apt-get install libssl-dev
```

## Nginx源码
Nginx的源码可以直接从官网下载，建议选择**stable**的版本。  

源码下载完成以后，解压并切换到目录。
```
tar -zxvf nginx-1.8.0.tar.gz
cd nginx-1.8.0
```

## Nginx模块和第三方模块
Nginx官方提供了很多[模块](http://wiki.nginx.org/Modules)。基于Nginx也有很多著名的第三方模块：
* [SPDY](https://en.wikipedia.org/wiki/SPDY)，在Nginx1.5.0版本中已经包含了该模块，之前是作为Nginx的补丁。
* [google pagespeed](https://github.com/pagespeed/ngx_pagespeed)，谷歌开发的模块，可以加快页面的载入时间，提高Web性能。
* [ModSecurity](http://www.modsecurity.org/)，一款开源的WAF。


## Nginx编译安装选项
在编译安装的时候，如果不想使用默认的配置，比如配置文件路径、日志文件路径等，需要在编译安装的时候指定选项，Nginx 1.8.0版本大约有140个选项。具体有哪些选项，可以使用`./configure --help`查看。   
总体看来，Nginx的安装选项可以分为以下几类：
* Path options
* Prerequisites options
* Module options
* Miscellaneous options

Nginx编译安装示例:
```
# Configure nginx.
#
# This is based on the default package in Debian. Additional flags have
# been added:
#
# * --with-debug: adds helpful logs for debugging
# * --with-openssl=$HOME/sources/openssl-1.0.1e: compile against newer version
#   of openssl
# * --with-http_spdy_module: include the SPDY module
./configure --prefix=/etc/nginx \
--sbin-path=/usr/sbin/nginx \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--pid-path=/var/run/nginx.pid \
--lock-path=/var/run/nginx.lock \
--http-client-body-temp-path=/var/cache/nginx/client_temp \
--http-proxy-temp-path=/var/cache/nginx/proxy_temp \
--http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
--http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
--http-scgi-temp-path=/var/cache/nginx/scgi_temp \
--user=nginx \
--group=nginx \
--with-http_ssl_module \
--with-http_realip_module \
--with-http_addition_module \
--with-http_sub_module \
--with-http_dav_module \
--with-http_flv_module \
--with-http_mp4_module \
--with-http_gunzip_module \
--with-http_gzip_static_module \
--with-http_random_index_module \
--with-http_secure_link_module \
--with-http_stub_status_module \
--with-mail \
--with-mail_ssl_module \
--with-file-aio \
--with-http_spdy_module \
--with-cc-opt='-g -O2 -fstack-protector --param=ssp-buffer-size=4 -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2' \
--with-ld-opt='-Wl,-z,relro -Wl,--as-needed' \
--with-ipv6 \
--with-debug \
--with-openssl=$HOME/sources/openssl-1.0.1g \
--add-module=/path/to/module
```
执行完`./configure`命令以后，`make`和`make install`即可。

## 总结
Nginx编译安装没有想象中的那么困难，也基本上没有遇到什么困难。但是还有很多后续的步骤要做，比如说设置开机启动，将Nginx设置为系统服务等。在之后会一步步完善该功能。  
这篇博客写的好没感觉~~
<br />
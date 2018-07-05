---
title: Nginx日志
categories: G-WebServer
permalink: nginx-logs
translate_title: nginx-log
date: 2015-11-05 14:37:32
tags:
description:
---
## 概述
日志在我们的开发中越来越重要，特别是对于已经上线的产品，日志可以帮助我们高效的进行排错，也可以对通过日志进行一些统计，比如统计当前网站的访客等。   

作为最流行的Web服务器之一，Nginx也提供了方便的日志功能。总的来说，Nginx提供了两种日志：访问日志(access_log)和错误日志(error_log)。本篇文章主要研究下Nginx的两种日志。

## error_log
Nginx配置文件中，使用`error_log`指令配置Nginx的错误日志，error_log指令包含在Nginx核心模块中。  

error_log配置的语法如下:
```
error_log log_file [ log_level ]
```
官方文档的详细说明如下：
```
Syntax:	error_log file | stderr | syslog:server=address[,parameter=value] | memory:size [debug | info | notice | warn | error | crit | alert | emerg];
Default:	
error_log logs/error.log error;
Context:	main, http, mail, stream, server, location
```

* log_file，参数用于指定错误日志的存储位置
* log_level，用于指定记录错误日志的级别，Nginx错误日志定义了一下几个错误级别：emerg、alert、crit、error、warn、notice、info、debug。

如果不想记录任何错误日志，需要将Nginx的输出重定向到`/dev/null`中，配置格式如下：
```
error_log /dev/null crit;
```

有一点需要注意的是，如果想开启`debug`级别的错误日志，需要在编译Nginx的时候，加上`--with-debug`选项。  

除了将Nginx错误日志记录到文件中，还可以将错误日志记录到syslog和内存中。

## access_log
上一部分说明了error_log属于Nginx核心模块，不同的是access_log属于Nginx的`ngx_http_log_module`模块。 

与access_log配置相关的指令有两个:`log_format`和`access_log`

### log_format
log_format指令用来设置日志文件的格式，Nginx有一种预定义的日志格式，称为`combined`，这也是一种比较通用的格式。  

`combined`格式的显示如下：
```
log_format combined '$remote_addr - $remote_user [$time_local]  '
		    '"$request" $status $body_bytes_sent '
		    '"$http_referer" "$http_user_agent"';
```
在这种配置中，以`$`开头的表示变量，其他的字符都会被当成字面值常量。   

log_format指令的格式如下:
```
log_format format_name string_describing_formatting;
```

我们可以使用Nginx核心模块支持的变量来定义想要的日志格式。Nginx支持的变量如下:[Embedded Variables](http://nginx.org/en/docs/http/ngx_http_core_module.html#variables)  

<br />
### access_log
access_log指令的格式如下:
```
access_log /path/to/log/location [ format_of_log buffer_size ];
```
access_log默认的配置格式为`combined`，我们也可以使用任何在log_formate中定义的格式。  
`buffer_size`是指Nginx在将日志写入到文本文件之前，可以在内存中占用的最大空间。  

我们也可以通过在配置中增加gzip来对日志进行压缩。方法如下:
```
access_log location format gzip;
```

不同于error_log，如果我们不想记录access_log，可以直接关闭access_log,方法如下:
```
access_log off;
```
<br />
### 日志格式变量清单
* $remote_addr   与$http_x_forwarded_for 用以记录客户端的ip地址
* $remote_user   ：用来记录客户端用户名称；
* $time_local  ： 用来记录访问时间与时区；
* $request  ：  用来记录请求的url与http协议；
* $status     ：  用来记录请求状态；成功是200，
* $body_bytes_s ent  ：记录发送给客户端文件主体内容大小；
* $http_referer  ：用来记录从那个页面链接访问过来的；
* $http_user_agent  ：记录客户端浏览器的相关信息；

还有其他变量，大家可以自行参考手册。

## 参考
* [LOGGING AND MONITORING](https://www.nginx.com/resources/admin-guide/logging-and-monitoring/)
* [How To Configure Logging and Log Rotation in Nginx on an Ubuntu VPS](https://www.digitalocean.com/community/tutorials/how-to-configure-logging-and-log-rotation-in-nginx-on-an-ubuntu-vps)


<br />
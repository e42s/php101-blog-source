---
title: 启用php-fpm status page
categories: G-WebServer
permalink: enable-php-fpm-status-page
translate_title: enable-php-fpm-status-page
date: 2015-11-11 15:41:57
tags:
description:
---
## 概述
无意中看到了PHP-FPM有一个status page，研究下。  

PHP-FPM内置了一个非常有用的状态页(status page)。  

## 开启状态页
### 编辑PHP-FPM配置文件
根据不同的Linux版本，PHP-FPM的配置文件路径有所不同，在centos中，PHP-FPM配置文件的路径为`/etc/php-fpm.d/www.conf`,在配置文件中找到`pm.status_path`配置项，取消注释，示例如下：
```
pm.status_path = /status
```
如果运行了多个PHP pool实例，也可以在/status前追加前缀。
<br />
### 编辑Nginx配置文件
在相应的server块中，增加如下配置:
```
location ~ ^/(status|ping)$ {
	  access_log off;
     allow 127.0.0.1;
     allow 1.2.3.4#your-ip;
     deny all;
     fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
     include fastcgi_params;
     fastcgi_pass 127.0.0.1:9000;
}
```
重启PHP-FPM和Nginx即可。

## 访问状态页
### 默认状态页
按照上述的步骤开启状态页之后，即可通过浏览器访问状态页,访问方式如下：`http://example.com/status`,内容如下：
```
pool:                 www
process manager:      dynamic
start time:           17/May/2013:13:54:02 +0530
start since:          886617
accepted conn:        1619617
listen queue:         0
max listen queue:     0
listen queue len:     0
idle processes:       28
active processes:     2
total processes:      30
max active processes: 31
max children reached: 0
slow requests:        0
```
<br />
### 全状态页
如果需要显示更详细的状态页面，可以在URL追加`?full`参数，如`http://example.com/status?full `,示例结果如下：
```
pid:                  1419692
state:                Idle
start time:           27/May/2013:20:06:12 +0530
start since:          287
requests:             32
request duration:     188927
request method:       GET
request URI:          /feed.php?uid=12997446135571490564
content length:       0
user:                 -
script:               /var/www/example.com/htdocs/feed.php
last request cpu:     5.29
last request memory:  524288
```
<br />
### 状态页输出格式
通过追加不同的参数，可以指定状态页以不同的格式输出。  
JSON格式:
```
http://example.com/status?json&full
```
HTML格式:
```
http://example.com/status?html&full
```
XML格式:
```
http://example.com/status?xml&full
```

## 总结
可以写一个脚本，通过获取本页内容来监控PHP进程的状态。

## 参考
* [Nginx – Enable PHP-FPM Status Page](https://rtcamp.com/tutorials/php/fpm-status-page/)
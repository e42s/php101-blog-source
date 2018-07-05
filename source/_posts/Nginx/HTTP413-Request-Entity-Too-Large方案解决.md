---
title: 'HTTP:413 Request Entity Too Large解决方案'
categories: A-PHP基础
permalink: 'htp:413,-request,-entity,-to,-large-solutions'
translate_title: 'http:413-request-entity-too-large-solution'
date: 2015-01-23 18:45:08
tags:
description:
---
## 概述
今天遇到一个问题，在PHP程序中上传图片出现了以下错误：**HTTP:413 Request Entity Too Large**。  

开发环境：CentOS + Nginx + PHP + MySql

## 解决方案
解决此问题，根据上传数据文件的大小，需要调节PHP和Nginx相关的参数配置。
<br />
### 配置PHP
PHP默认上传文件大小限制为2M，如果超出2M你需要修改PHP配置文件php.ini里面的参数。 
```
post_max_size = 8M （表单提交的最大限制，此项不是限制上传单个文件的大小，而是针对整个表单提交的数据进行限制。）

upload_max_filesize = 2M （上传的单个文件的最大限制）
``` 
需要保证 post_max_size >= upload_max_filesize ，也就是前者不小于后者。  
修改之后一定要重启 php-fpm。
<br />
### 配置Nginx
除了修改 php 配置，你也需要修改nginx配置文件nginx.conf。
```
client_max_body_size 30M;
```  
其中30M可以根据需要上传文件大小自行设定。  
修改之后一定要重新载入nginx(service nginx restart)。


## 总结
今天解决了好几个小问题，挨个记录下~~   
~~进步不用多，每天一点点~~
<br />
<br />

   
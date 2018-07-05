---
title: Nginx变量和返回状态码
categories: G-WebServer
permalink: nginx-variables-and-return-status-codes
translate_title: nginx-variable-and-return-state-code
date: 2015-11-17 10:30:13
tags:
description:
---
## 概述
这篇文章简单介绍下Nginx的变量和返回状态码。

## Nginx变量
在Nginx配置文件中我们可以使用变量，这里所说的变量与其他编程语言中的变量概念一样，不过多做解释。  

在Nginx配置文件中，变量是以`$`符号开头的。  

Nginx有许多有预定义的变量，如[HTTP核心模块中的变量](http://nginx.org/en/docs/http/ngx_http_core_module.html?&_ga=1.108377733.667418771.1436862331#variables)。此外，还可以使用[set](http://nginx.org/en/docs/http/ngx_http_rewrite_module.html?&_ga=1.84327838.667418771.1436862331#set),[map](http://nginx.org/en/docs/http/ngx_http_map_module.html?&_ga=1.150511025.667418771.1436862331#map),[geo](http://nginx.org/en/docs/http/ngx_http_geo_module.html?&_ga=1.150511025.667418771.1436862331#geo)等指令自定义变量。   

一篇讲解Nginx变量的文章：[agentzh's Nginx Tutorials](https://openresty.org/download/agentzh-nginx-tutorials-en.html)


## Nginx返回状态码
在某种应用场景下，我们需要根据用户的请求直接返回错误状态码或者跳转页面。这种情况可以使用`return`指令，return指令的使用方式如下：
```
Syntax:	return code [text];
return code URL;
return URL;
```
return指令的第一个参数是**状态码**，第二个参数是url或者要返回的内容。

1、返回**404**状态码示例：
```
location /wrong/url {
    return 404;
}
```
2、返回**301**状态码示例：
```
location /permanently/moved/url {
    return 301 http://www.example.com/moved/here;
}
```

## 总结
这篇文章只简单介绍Nginx的变量和返回状态码，其中Nginx变量的学习需要第三方http_echo模块的支持。
<br />


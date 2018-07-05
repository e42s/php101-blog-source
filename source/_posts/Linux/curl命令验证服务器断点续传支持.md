---
title: curl命令验证服务器断点续传支持
categories: D-Linux
tags: curl
permalink: the-curl-comand-authentication-server-htp
translate_title: curl-command-verification-server-breakpoint-renewal-support
date: 2015-02-25 17:22:26
description:
---
## 概述
之前工作中遇到一个问题是爬虫下载大文时是否支持断点续传，一直以为断点续传是客户端实现的，后来发现不是。断点续传与资源所在的服务器是否支持有关，与客户端的功能无关。这篇文章只介绍如何验证要下载的资源是否支持断点续传，其他内容在随后的文章中会介绍。

## 验证是否支持断点续传
要验证服务器是否支持断点续传，使用curl命令即可，命令如下：
```
curl -I 'url'
```
看返回的http头信息，如果有 Accept-Ranges: bytes 表示服务器支持Range请求，以及服务器所支持的单位是字节(这也是唯一可用的单位)，同时说明服务器支持断点续传。如果有 Accept-Ranges: none 响应头表示服务器不支持范围请求，也就是不支持断点续传。


## 总结
会抽时间配置下Nginx服务器，实现断点续传的配置。
<br />
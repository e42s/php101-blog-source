---
title: >-
  fsockopen:unable to connect to xxx:-1(Unable to find the socket transport http
  – did you forget to enable it when you configured PHP?) in xxx.php
categories: A-PHP中高级
permalink: >-
  fsockopen:unable,-to,-conect,-to,-x:-1-(unable,-to,-find,-the,-socket,-transport,-htp,-did,-you,-forget,-to,-enable,-it,-when,-you,-configured,-php,-etc.)-in,-x.php
translate_title: >-
  fsockopen:unable-to-connect-to-xxx:-1-(unable-to-find-the-socket-transport-transport---*-(?
date: 2015-11-11 16:16:33
tags:
description:
---
## 概述
想做个简单的聊天室，看一些示例程序里都用到了`fsockopen`函数，本来以为会是个很简单的函数，没想到一次用就踩坑了，报了如下错误:
```
fsockopen:unable to connect to xxx:-1(Unable to find the socket transport http – did you forget to enable it when you configured PHP?) in xxx.php
```

## 示例代码
```
<?php
    $host = 'http://www.baidu.com';
    $host = 'www.baidu.com';
    $host = '115.239.211.112';
    $socket = fsockopen($host, 80);
?>
```

## 解决方案
如果连接的参数带有`http`前缀的话，则会报上述错误，去掉`http`或者使用IP地址直接连接，即可避免该错误。  

看了晚上的解决方案：[http://stackoverflow.com/questions/9965430/fsock-unable-to-find-the-socket-transport-http](http://stackoverflow.com/questions/9965430/fsock-unable-to-find-the-socket-transport-http)说是fsockopen不支持layer5+之上的协议。

## 参考
* [http://stackoverflow.com/questions/9965430/fsock-unable-to-find-the-socket-transport-http](http://stackoverflow.com/questions/9965430/fsock-unable-to-find-the-socket-transport-http)
<br />

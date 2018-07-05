---
title: 理解PHP中的流(streams)
categories: A-PHP中高级
tags:
  - streams
permalink: understanding-the-flow-in-php-(streams)
translate_title: understand-the-flow-in-php-(streams)
date: 2014-09-22 22:23:23
---
## 概述
流(streams)是PHP4.3版本引入的一个特性，主要是为了统一文件、sockets以及其他类似资源的工作方法。PHP4.3距今已经有很长时间了，但是很多程序员似乎都不能正确使用PHP中的流，当然这也包括我。以前也在一些程序中遇到过流的使用，如php://input，但是一直没机会整理，今天就把这部分知识整理下。<br />  
流是由PHP提供的资源，可以供我们透明的使用，而且流是一个非常强大的工具。适当的在程序中使用流，可以将我们的程序带到一个新的高度。<br />  
PHP手册中对流的描述如下：
> Streams were introduced with PHP 4.3.0 as a way of generalizing file, network, data compression, and other operations which share a common set of functions and uses. In its simplest definition, a stream is a resource object which exhibits streamable behavior. That is, it can be read from or written to in a linear fashion, and may be able to fseek() to an arbitrary locations within the stream.

<br />
每一种流都实现了一个包装器(wrapper)，包装器包含一些额外的代码用来处理特殊的协议和编码。PHP提供了一些[内置的包装器](http://php.net/manual/en/wrappers.php)，我们也可以很轻松的创建和注册自定义的包装器。我们甚至可以使用上下文(contexts)和过滤器来改变和增强包装器。

## 流基础知识
PHP中流的形式如：`<scheme>://<target>`。`<scheme>`是包装器的名字，`<target>`的内容取决于不同的包装器语法。  
默认的包装器是`file://`,也就是说每次我们访问文件系统的时候都使用了流。例如，我们可以使用如下两种方式来读取文件：`readfile('/path/to/somefile.txt') `和`readfile('file:///path/to/somefile.txt')`，使用这两种方式读取文件，可以得到相同的结果。
正如前面所说，PHP提供了一些内置的包装器、协议和过滤器。查看我们的机器上安装了哪些包装器，可以使用如下几个函数：
```
<?php
    var_dump(stream_get_transports());
    var_dump(stream_get_wrappers());
    var_dump(stream_get_filters());
?>
```
我本地的环境输出内容如下:
```
array (size=8)
  0 => string 'tcp' (length=3)
  1 => string 'udp' (length=3)
  2 => string 'unix' (length=4)
  3 => string 'udg' (length=3)
  4 => string 'ssl' (length=3)
  5 => string 'sslv3' (length=5)
  6 => string 'sslv2' (length=5)
  7 => string 'tls' (length=3)
array (size=12)
  0 => string 'https' (length=5)
  1 => string 'ftps' (length=4)
  2 => string 'compress.zlib' (length=13)
  3 => string 'compress.bzip2' (length=14)
  4 => string 'php' (length=3)
  5 => string 'file' (length=4)
  6 => string 'glob' (length=4)
  7 => string 'data' (length=4)
  8 => string 'http' (length=4)
  9 => string 'ftp' (length=3)
  10 => string 'phar' (length=4)
  11 => string 'zip' (length=3)
array (size=12)
  0 => string 'zlib.*' (length=6)
  1 => string 'bzip2.*' (length=7)
  2 => string 'convert.iconv.*' (length=15)
  3 => string 'string.rot13' (length=12)
  4 => string 'string.toupper' (length=14)
  5 => string 'string.tolower' (length=14)
  6 => string 'string.strip_tags' (length=17)
  7 => string 'convert.*' (length=9)
  8 => string 'consumed' (length=8)
  9 => string 'dechunk' (length=7)
  10 => string 'mcrypt.*' (length=8)
  11 => string 'mdecrypt.*' (length=10)
```
另外，我们可以自定义或者使用第三方的流。
## php://包装器
PHP有它自己的访问输入/输出(I/O)流的包装器。PHP有基本的`php://stdin `,`php://stdout `,`php://stderr `包装器对应默认的I/O资源。还有一个`php://input`流，它是一个只读的流，流内容是post请求的数据。当我们将数据放在一个post请求的body体内用来请求一个远程服务的时候，这个流特别好用。<br />
因为`php://input`是最常用到的流，所以这里列出一些知识点：
> 1.php://input可以读取没有处理过的POST数据。相较于$HTTP_RAW_POST_DATA而言，它给内存带来的压力较小，并且不需要特殊的php.ini设置。php://input不能用于enctype=multipart/form-data  
> 
> 2.仅当Content-Type为application/x-www-form-urlencoded且提交方法是POST方法时，$_POST数据与php://input数据才是”一致”（打上引号，表示它们格式不一致，内容一致）的。其它情况，它们都不一致
> 
> 3.php://input读取不到$_GET数据。是因为$_GET数据作为query_path写在http请求头部(header)的PATH字段，而不是写在http请求的body部分。

## 流上下文(Stream Contexts)
这部分内容在编程中几乎没有遇到过，本人研究起来也比较吃力，大家感兴趣的话可以自行百度。

## 总结
流在平时的编程中用到的并不是很多，在使用xml-rpc的时候，server端获取client数据，主要是通过php输入流input，这是一种常用的场景。黑客在入侵网站的时候，也可能会用到这部分内容。

## 参考
1.[Understanding Streams in PHP](http://www.sitepoint.com/%EF%BB%BFunderstanding-streams-in-php/)  
2.[Using PHP Streams Effectively](http://www.sitepoint.com/using-php-streams-effectively/)  
3.[Introduction to PHP streams](http://www.sk89q.com/2010/04/introduction-to-php-streams/)  
4.[PHP输入流php://input ](http://blog.csdn.net/lxzo123/article/details/6701655)
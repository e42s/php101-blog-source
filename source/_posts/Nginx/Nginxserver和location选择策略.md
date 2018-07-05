---
title: 'Nginx:server和location选择策略'
categories: G-WebServer
permalink: 'nginx:server-and-location-selection-policies'
translate_title: 'nginx:server-and-location-selection-strategies'
date: 2015-11-12 20:06:42
tags:
description:
---
## 概述
这篇文章主要研究下Nginx处理客户端请求背后的细节。    

Nginx配置文件必须有一个server指令。在Nginx配置文件中，server指令用来定义一个虚拟主机(virtual server)，每个虚拟主机都会处理一种特定类型的请求。通常Nginx配置文件中，会有多个server指令，Nginx会根据客户端请求的域名、端口和IP来决定调用哪个server配置型来处理客户端请求。   

location指令定义在server指令的上下文环境中，它定义了Nginx如何处理对于不同资源和URL的请求。   

本篇文章主要涉及Nginx配置中关于`server`和`location`的配置。  

## Nginx是如何判定哪个server来处理一个请求
由于在Nginx中可以定义多个虚拟主机，因此当接收到一个请求时，它需要有一种策略来决定采用哪个server来响应请求。Nginx采用定义好的检查系统来找到最匹配的server指令来处理客户端请求，在该过程中，Nginx最关心的是`listen`指令和`server_name`指令。  
<br />
### 解析listen指令
首先，Nginx会查看当前请求的IP和端口，如果匹配到某个`listen`指令中监听的IP和端口，那么该`listen`指令所在的server就会被加入到一个匹配列表中。   

`listen`指令定义了server块将要响应的IP和端口。默认的，如果server块指令中忽略了`listen`指令，那么`listen`指令将会使用默认监听参数：**0.0.0.0:80**(如果Nginx以非root用户运行，则是**0.0.0.0:8080**)。如果server指令中忽略了`listen`指令，那么该server就能响应任何网卡上在80端口的请求，但是这种配置在server的选择过程中，优先级并不高。  

`listen`指令的配置有以下几种形式：
* IP:PORT，IP和端口组合
* 仅有IP，则默认监听80端口
* 仅有端口，监听所有网卡的该端口
* Unix socket路径

当Nginx决定将请求发送给某个server时，它首先会根据`listen`指令的定义，尝试以下规则：
* Nginx首先会将“不完整”的listen指令，采用默认值补全，这样就能根据IP和端口对每个server块进行判断。
* 接着，Nginx会收集所有与请求中的IP和端口匹配的server块。这意味着如果有匹配的server块，那么任何将`0.0.0.0`作为IP的server块都不会被选择。但是，无论如何，端口都应该是被精确匹配的。
* 如果只有一个匹配的server块，那么Nginx就会采用当前配置块来处理请求。如果有多个匹配的server块，Nginx会评估server指令中的`server_name`指令，来进行下一步的匹配。  

有一点需要明白的是，只有当多个`listen`指令满足同一个请求时，Nginx才会去评估`server_name`指令进行进一步的选取。在下面的例子中，Nginx永远都会采用第一个server块来处理请求，而且会忽略掉第二个server块中的`server_name`指令:
```
server {
    listen 192.168.1.10;

    . . .

}

server {
    listen 80;
    server_name example.com;

    . . .

}
```
<br >
### 解析server_name指令
如上一步所说，如果有多个与请求的相匹配的`listen`指令，Nginx会进一步检查`server_name`指令。Nginx会检查请求中的“Host”头，“Host”头中包含了客户端请求的域名或IP地址。 

Nginx会尝试从满足匹配条件的候选server块中找出最匹配的server来处理请求，这种选择通常遵循如下流程：
* Nginx首先尝试查找与Host相匹配的server_name，如果能找到一个，则选择与其关联的server来响应客户端请求。如果找到多个，则选择第一个来响应。
* 如果没有找到，Nginx会尝试查找server_name中以通配符(*)开头的server块来作为响应。如果找到一个，则选择与其关联的server块响应。如果找到多个，则选择名字最长的server块来响应。  
* 如果没有找到，Nginx会接着查找server_name中一个通配符结尾的server块来作为响应。
* 如果还是没有找到，Nginx会查找server_name中包含正则表达式的server块(以~开头)。  
* 如果没有找到，Nginx会选择默认的server来响应客户端请求。  

对于满足匹配条件的所有server块，如果按照上述流程依旧没有找到合适的server块，那么默认的server块会被使用。默认的server通过在listen指令中添加`default_server`来定义。格式如下：
```
listen 80 default_server;
```

## 匹配location
`location`指令嵌套在server块指令中(或者其他location指令中)。location指令用来决定如何处理请求的资源(IP和端口之后的内容)。  

location指令的配置格式如下：
```
location optional_modifier location_match {

    . . .

}
```
**location_match**部分定义了Nginx检查请求的资源的方式。**optional_modifier**影响了Nginx选择所匹配的location块。  

optional_modifier会以下列方式影响location的解析：
* (none)，如果没有optional_modifier，location被解析为前缀匹配。这意味着给定的location需要与请求的URI开始部分是匹配的。  
* =，这表示给定的location需要请求的URI完全匹配，也就是精确匹配。
* ~，这表示大小写敏感的正则表达式匹配。
* ~*，这表示大小写不敏感的正则表达式匹配。
* ^*，这表示最佳的非正则表达式匹配。

location优先级说明:
* 等号类型（=）的优先级最高。一旦匹配成功，则不再查找其他匹配项。
* ^~类型表达式。一旦匹配成功，则不再查找其他匹配项。
* 正则表达式类型（~ ~*）的优先级次之。如果有多个location的正则能匹配的话，则使用正则表达式最长的那个。
* 常规字符串匹配类型。按前缀匹配。

<br />
### Nginx如何选择location
Nginx选择location的方式，与上述选择server的方式相同。具体来说，Nginx使用如下策略来选择location块：
* Nginx会检查所有基于前缀的匹配(这不包含正则表达式)。它会将每一个location与完整的URI请求相匹配。
* 首先，Nginx会查找精确匹配。如果能找到一个精确匹配，则使用该location。精确匹配指(=)。
* 如果没有找到精确匹配，Nginx会查找非精确匹配，并选择所有匹配中最长的匹配
* 如果最长匹配以^~开头，则停止查找
* 如果还没有找到，则查找预制匹配的正则表达式location。

## 总结
这篇文章拖得时间真长，写的也很累，一方面英语太渣，一方面Nginx这方面的知识不足。以后回来把这篇文章补全。  

## 参考
* [CONFIGURING NGINX PLUS AS A WEB SERVER](https://www.nginx.com/resources/admin-guide/nginx-web-server/)
* [Understanding Nginx Server and Location Block Selection Algorithms](https://www.digitalocean.com/community/tutorials/understanding-nginx-server-and-location-block-selection-algorithms)
* [nginx location在配置中的优先级](http://saebbs.com/forum.php?mod=viewthread&tid=36443&extra=page%3D1)
* [https://linux.cn/article-5714-weibo.html](https://linux.cn/article-5714-weibo.html)
<br />
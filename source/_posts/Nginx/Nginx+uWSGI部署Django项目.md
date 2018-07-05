---
title: Nginx+uWSGI部署Django项目
categories: G-WebServer
permalink: nginx-uwsgi-deployment-django-project
translate_title: nginx+uwsgi-deploys-the-django-project
date: 2015-07-15 15:51:07
tags:
description:
---
## 概述
遇到一个需求，使用Nginx+uWSGI部署Djang项目，本来以为这个环境的搭建与PHP环境的搭建差不多，没想到遇到了各种问题，特此记录。

## 安装Django
首先需要安装Django，Django的安装可以通过Python的pip包管理器。如果环境中没有pip，使用相应的Linux包管理器安装即可，比如Ubuntu使用apt、centos使用yum。  

安装Django
```
pip install Django
```
初始化一个Django项目，如果是已经完成的项目，仅仅需要部署，可以省略此步骤
```
django-admin.py startproject mysite
cd mysite
```

## 安装uWSGI
### 安装
使用pip包安装uWSGI:
```
pip install uwsgi
```
<br />
### 测试uWSGI
安装完uWSGI以后，可以测试下其是否安装成功，首先在项目根目录下创建一个`test.py`文件，内容如下:
```
# test.py
def application(env, start_response):
    start_response('200 OK', [('Content-Type','text/html')])
    return [b"Hello World"] # python3
    #return ["Hello World"] # python2
```
<br />
### 运行uWSGI
```
uwsgi --http :8000 --wsgi-file test.py
```
通过浏览器访问8000端口，可以看到浏览器输出了`hello world`。
<br />
### 测试Django
```
python manage.py runserver 0.0.0.0:8000
```
这一步用来测试Django项目是否有问题

## Nginx
Nginx的安装可以使用包管理器安装，也可以直接编译安装。  
### uwsgi_params
首先我们需要拷贝一份`uwsgi_params`文件到项目根目录下（也可以不拷贝，在之后的配置文件中指定相应的路径即可）  
`uwsgi_params`文件位于nginx的配置目录下，一般在`/etc/nginx/uwsgi_params`，如果没有，可以直接下载，地址:[https://github.com/nginx/nginx/blob/master/conf/uwsgi_params](https://github.com/nginx/nginx/blob/master/conf/uwsgi_params)。
<br />
### 配置文件
配置文件在项目的根目录下创建，也可以直接在Nginx的配置目录下配置，这个根据自己的需求即可。配置文件格式如下：
```
upstream django {
    server unix:///path/to/your/mysite/mysite.sock; # for a file socket
    # server 127.0.0.1:8001; # for a web port socket (we'll use this first)
}

server {
    listen      8000;
    server_name .example.com; # substitute your machine's IP address or FQDN
    charset     utf-8;

    client_max_body_size 75M;   # adjust to taste

    # Django media
    location /media  {
        alias /path/to/your/mysite/media;  # your Django project's media files - amend as required
    }

    location /static {
        alias /path/to/your/mysite/static; # your Django project's static files - amend as required
    }

    # Finally, send all non-media requests to the Django server.
    location / {
        uwsgi_pass  django;
        include     /path/to/your/mysite/uwsgi_params; # the uwsgi_params file you installed
    }
}
```
部署静态文件
修改Django项目中的配置文件：
```
STATIC_ROOT = os.path.join(BASE_DIR, "static/")
```
并运行如下命令：
```
python manage.py collectstatic
```
### uWSGI以socket方式运行
首先运行如下命令测试uWSGI
```
uwsgi --socket :8001 --wsgi-file test.py
```
运行成功后，可以修改Nginx配置文件，内容如下:
```
server unix:///path/to/your/mysite/mysite.sock; # for a file socket
```
<br />
### 使用Nginx和uWSGI运行Django
```
uwsgi --socket mysite.sock --module mysite.wsgi 
```
一般需要以守护方式运行uWSGI，可以加上`--daemonize`参数，如下:
```
uwsgi --socket mysite.sock --module mysite.wsgi --daemonize true
```

## 配置文件方式运行uWSGI
以命令行的方式运行uWSGI维护起来不太方便，一般更倾向于使用配置文件来运行uWSGI。  
在项目的根目录创建一个`demo.ini`文件，内容如下:
```
[uwsgi]

# Django-related settings
# the base directory (full path)
chdir           = /path/to/your/project
# Django's wsgi file
module          = project.wsgi

# process-related settings
# master
master          = true
# maximum number of worker processes
processes       = 10
# the socket (use the full path to be safe
socket          = /path/to/your/project/mysite.sock
# ... with appropriate permissions - may be needed
# chmod-socket    = 664
# clear environment on exit
vacuum          = true
daemonize = true
```
运行uWSGI，命令如下：
```
uwsgi --ini mysite_uwsgi.ini
```

## 总结
配置的时候因为粗心导致了折腾了很久，有一点不明白得是uWSGI为什么要使用Nginx的`upstream`。

## 参考
[http://uwsgi-docs.readthedocs.org/en/latest/tutorials/Django_and_nginx.html](http://uwsgi-docs.readthedocs.org/en/latest/tutorials/Django_and_nginx.html)




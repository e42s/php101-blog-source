---
title: Django管理静态文件
categories: F-Python
permalink: django-manages-static-files
translate_title: django-manages-static-files
date: 2016-11-09 00:16:07
tags:
description:
---
## 概述
用了django将近一年的时间了，期间也遇到了各种问题，但是最大的问题之一就是django的静态文件管理，终于下定决心把django关于静态文件的坑给踩了。   


## django静态文件分类
`django`把静态文件分为两类: `static`和`media`，django是按照是否与用户交互来进行区分的。

* `STATIC_ROOT`配置项用来管理静态资源文件。应用页面需要的（JS, CSS, Image...），这些相对来说是提供给用户的，即这些文件（非严格讲）没有与用户进行互动，开发者产生这些文件为访问用户服务，用户只是单纯的享受应用服务。它们通过`manage.py collectstatic`命令汇集到 `settings.STATIC_ROOT`目录，并通过`settings.STATIC_URL`指定的路径访问。

* `MEDIA_ROOT` 配置项用来管理**用户上传**的文件。通常经由`FileFields`字段上传，它们保存在 `settings.MEDIA_ROOT`指定的目录下，通过`settings.MEDIA_URL`指定的路径访问。

## staticfiles
在`Django 1.3`中，`staticfiles`默认是启用的。如果是从1.3之前的代码迁移过来，启用代码如下：

```
INSTALLED_APPS += ('django.contrib.staticfiles',)
TEMPLATE_CONTEXT_PROCESSORS += ('django.core.context_processors.static',)
```

## 静态文件配置项
### STATIC_ROOT 配置项
用来指定执行`manage.py collectstatic`时静态文件存放的路径。在生成环境中，集中存放静态资源有利于使用`Lighttpd/Nginx/apache2`托管静态资源。`STATIC_ROOT`文件夹是用来将所有`STATICFILES_DIRS`中所有文件夹中的文件，以及各app中`static`的文件都复制过来。为了方便调试，通常设置如下：

```
PROJECT_DIR = os.path.dirname(os.path.dirname(__file__))
BASE_DIR = os.path.dirname(PROJECT_DIR)
  
STATIC_ROOT = os.path.join(BASE_DIR, 'run', 'static')
```
> STATIC_ROOT可以根据情况自行修改路径。

### STATIC_URL配置项
django模板中，可以引用{{STATIC_URL}}变量避免把路径写死。通常使用默认设置：

```
STATIC_URL = '/static/'
```

### STATICFILES_DIRS配置项
在执行`manage.py collectstatic`， django除了搜集每个已经安装app中的static文件夹，django还会搜集`STATICFILES_DIRS`定义的文件列表。

### STATICFILES_STORAGE配置项
`STATICFILES_STORAGE`配置项用来控制文件被聚集起来的方式。默认值是`django.contrib.staticfiles.storage.StaticFilesStorage `，表示将文件集合在`STATIC_ROOT`配置项指定的文件夹中。


### STATICFILES_FINDERS配置项
默认配置: 

```
STATICFILES_FINDERS = (
    'django.contrib.staticfiles.finders.FileSystemFinder',
    'django.contrib.staticfiles.finders.AppDirectoriesFinder',
#    'django.contrib.staticfiles.finders.DefaultStorageFinder',
)
```

* `FileStstemFinder`用来用STATICFILES_DIRS【默认为空】指定的路径中查找额外的静态文件。像jquery，bootstrap等这样公用的资源文件都是在多个不同的app中共用的，django提供了公有的目录来放这些文件，这个配置参数就是：STATICFILES_DIRS。
* `AppDirectoriesFinder`从INSTALLED_APPS元组中的APP所在包的static目录中查找资源文件。

`STATICFILES_FINDERS`被用来寻找源静态文件。`AppDirectoriesFinder`用来在每个app中的`static`文件搜索静态文件，如: `$app_name/static/ `。`FileSystemFinder`用来搜索在` STATICFILES_DIRS`配置项中指定的文件夹列表中寻找静态文件。


### ADMIN_MEDIA_PREFIX配置项
ADMIN_MEDIA_PREFIX必须为如下配置，以便staticfiles能够正确找到django.contrib.admin的静态资源：

```
ADMIN_MEDIA_PREFIX = STATIC_URL + 'admin/'
```

在模板中使用STATIC_URL：

```
<link rel="stylesheet" href="{{ STATIC_URL }}css/core.css">
```

## 开发环境
### 在自定义的STATICFILES_DIRS寻找静态文件
使用方式：  

* 保证`django.contrib.staticfiles`在`INSTALLED_APPS`中
* 在`TEMPLATES`配置中添加`django.template.context_processors.static`
* 在模板中使用`STATIC_URL`变量，eg：`<img src="{{STATIC_URL}}girl.png>`。

> 本文末尾的参考文献中，有一篇文章建议不要使用STATIC_URL变量。

### 在自定义的 STATIC_ROOT 寻找静态文件
如果你想访问静态文件时，静态文件在`STATIC_ROOT`中寻找,使用如下方式:

```
     urlpatterns += [
         url(r'^static/(?P<path>.*)$', 'django.views.static.serve', {'document_root': settings.STATIC_ROOT}),
     ]
```

这种方法不推荐，理由：开发过程中需要不断的执行`./manage.py collectstatic`。

## 生产环境
生产环境不同开发环境，生产环境下（DEBUG=False）,`django.contrib.staticfiles`是不起任何作用的，也就说`django.contrib.staticfiles`只对开发环境（DEBUG=True）开启。  

> With debug turned off Django won't handle static files for you any more - your production web server (Apache or something) should take care of that.

有如下两种方式在生产环境下依然可以让django自带的方式支持静态文件：  

第一种方式:  

```
python manage.py runserver --insecure
```

第二种方式:

```
if settings.DEBUG is False:
     urlpatterns += [
         url(r'^static/(?P<path>.*)$', 'django.views.static.serve', {'document_root': settings.STATIC_ROOT}),
     ]

```
此方法开发过程中需要不断的执行`./manage.py collectstatic`。
 



## 参考
* [Django | 静态文件处理](https://segmentfault.com/a/1190000004232816)
* [django静态文件配置](http://www.cnblogs.com/starof/p/4682812.html)
* [區別 Django 中的 STATIC_ROOT 與 STATICFILES_DIRS](http://justbm.blogspot.jp/2012/08/django-staticroot-staticfilesdirs.html)
* [Django and Static Files](http://blog.doismellburning.co.uk/django-and-static-files/)
* [Stop Using STATIC_URL in Templates](http://staticfiles.productiondjango.com/blog/stop-using-static-url-in-templates/)
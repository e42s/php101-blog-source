---
title: django ajax post 403 forbidden
categories: F-Django
permalink: django-ajax-post-403-forbiden
translate_title: django-ajax-post-403-forbidden
date: 2016-05-12 11:34:23
tags:
description:
---
## 概述
在使用Django时，使用POST方法发ajax请求会报`django ajax post 403 forbidden`错误，出现这个错误的原因是Django为了安全增加了`CSRF_TOKEN`，解决方法是在请求的时候加上csrf_token.


## 解决方法
参考stackoverflow上的一个回答:
```
you can actually pass this along with your data {csrfmiddlewaretoken:'{{csrf_token}}' } , it works all the time
```

## 参考
* [django ajax post 403 forbidden](http://stackoverflow.com/questions/13035412/django-ajax-post-403-forbidden)

<br />
---
title: 一台电脑使用多个GitHub账号
categories: Y-Git
permalink: one-computer-uses-multiple-github-acounts
translate_title: a-computer-uses-multiple-github-accounts
date: 2016-03-18 15:01:14
tags:
description:
---
## 概述
博客使用的是Github的Pages服务，之前想建立两个博客，一个是安全类的，一个是开发类的，无奈的是一台电脑只能使用一个Github账号，前一段无意中看到一篇文章，把这个问题解决了，现在记录下。


## 使用步骤

### 生成ssh key
这一步就不多说了，可以参考我的上一篇文章:[如何生成ssh-key以及使用多个ssh-key](http://www.php101.cn/2014/10/12/%E5%A6%82%E4%BD%95%E7%94%9F%E6%88%90ssh-key%E4%BB%A5%E5%8F%8A%E4%BD%BF%E7%94%A8%E5%A4%9A%E4%B8%AAssh-key/)。

### 修改配置文件
修改ssh的配置文件，如下：
```
Host github.com
  HostName github.com
  User git
  Port 22
  IdentityFile ~/.ssh/id_rsa  # 主账号所使用的 SSH 秘钥
  TCPKeepAlive yes
  IdentitiesOnly yes
Host github.com.sub  # (1) 副账号：可任意命名
  HostName github.com
  User git
  Port 22
  IdentityFile ~/.ssh/id_rsa_sub  # (2) 副账号所使用 SSH 秘钥路径
  TCPKeepAlive yes
  IdentitiesOnly yes
```


## 总结
博客断更了一段时间，从今天开始接着更新，(⊙o⊙)…努力吧，少年。


## 参考
* [一台电脑使用多个 Github 账户](http://achillessatan.github.io/2016/01/29/2016012902/)
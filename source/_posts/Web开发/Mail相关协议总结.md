---
title: Mail相关协议总结
categories: E-Web开发
tags: mail
permalink: mail-related-protocol-sumary
translate_title: summary-of-mail-related-protocols
date: 2015-03-04 15:10:38
description:
---
## 概述
这篇文章主要用来记录关于Mail（邮件）相关的一些协议。

## SMTP
### 什么是SMTP
SMTP称为简单Mail传输协议(Simple Mail Transfer Protocal),目标是向用户提供高效、可靠的邮件传输。SMTP的一个重要特点是它能够在传送中接力传送邮件，即邮件可以通过不同网络上的主机接力式传送。工作在两种情况下：一是电子邮件从客户机传输到服务器；二是从某一个服务器传输到另一个服务器。  

SMTP是个请求/响应协议，它监听25号端口，用于接收用户的Mail请求，并与远端Mail服务器建立SMTP连接。
<br />
### SMTP工作机制
SMTP通常有两种工作模式：发送SMTP和接收SMTP。具体工作方式为：发送SMTP在接到用户的邮件请求后，判断此邮件是否为本地邮件，若是直接投送到用户的邮箱，否则向dns查询远端邮件服务器的MX纪录，并建立与远端接收SMTP之间的一个双向传送通道，此后SMTP命令由发送SMTP发出，由接收SMTP接收，而应答则反方面传送。一旦传送通道建立，SMTP发送者发送MAIL命令指明邮件发送者。如果SMTP接收者可以接收邮件则返回OK应答。SMTP发送者再发出RCPT命令确认邮件是否接收到。如果SMTP接收者接收，则返回OK应答；如果不能接收到，则发出拒绝接收应答(但不中止整个邮件操作)，双方将如此重复多次。当接收者收到全部邮件后会接收到特别的序列，如果接收者成功处理了邮件，则返回OK应答。
<br /> 
### 端口
* Port 25 - this is the default SMTP non-encrypted port
* Port 2525 - this port is opened on all SiteGround servers in case port 25 is filtered (by your ISP for example) and you want to send non-encrypted emails with SMTP
* Port 465 - this is the port used, if you want to send messages using SMTP securely

## POP
### 什么是POP
POP的全称是 Post Office Protocol ，即邮局协议，用于电子邮件的接收，它使用TCP的110端口，现在常用的是第三版 ，所以简称为 POP3。POP3仍采用Client/Server工作模式，。当客户机需要服务时，客户端的软件(OutlookExpress或FoxMail)将与POP3服务器建立TCP连接，此后要经过POP3协议的三种工作状态，首先是认证过程，确认客户机提供的用户名和密码，在认证通过后便转入处理状态，在此状态下用户可收取自己的邮件或做邮件的删除，在完成响应的操作后客户机便发出quit命令，此后便进入更新状态，将做删除标记的邮件从服务器端删除掉。到此为止整个POP过程完成。   
<br />
### POP3协议的不足
POP作为Internet上邮件的第一个离线协议标准，允许用户从服务器上把邮件下载到本地主机上，同时删除保存在邮件服务器上的邮件，从而使用户不必长时间地与邮件服务器连接，很大程度上减少了服务器和网络的整体开销。  

但POP3有其天生的缺陷，即当用户接收电子邮件时，所有的信件都从服务器上清除并下载到客户机。在整个收信过程中，用户无法知道邮件的具体信息，只有照单全收入硬盘后，才能慢慢浏览和删除。这使用户几乎没有对邮件接收的控制决定权。一旦碰上邮箱被轰炸，或有比较大的邮件，用户不能通过分析邮件的内容及发信人地址来决定是否下载或删除，从而造成系统资源的浪费。而IMAP协议不但可以克服POP3的缺陷，而且还提供了更强大的功能。
<br />
### 端口
* Port 110 - this is the default POP3 non-encrypted port
* Port 995 - this is the port you need to use if you want to connect using POP3 securely

## IMAP
### 什么是IMAP
IMAP是Internet Message Access Protocol的缩写，顾名思义，主要提供的是通过Internet获取信息的一种协议。IMAP象POP那样提供了方便的邮件下载服务，让用户能进行离线阅读，但IMAP能完成的却远远不只这些。IMAP提供的摘要浏览功能可以让你在阅读完所有的邮件到达时间、主题、发件人、大小等信息后才作出是否下载的决定。  

IMAP (Internet Message Access Potocol) 是一种用于邮箱访问的协议，使用IMAP协议可以在Client端管理 Server上的邮箱，它与pop不同，邮件是保留在服务器上而不是download到本地，在这一点上IMAP是与Webmail相似的。但IMAP有比Webmail更好的地方，它比webmail更高效和安全，可以离线阅读等等，如果想试试可以用Outlook Express，只要配好一个帐号,将我的邮件接收服务器设置为IMAP服务器就可以了。  

IMAP(Internet消息访问协议)是与POP3对应的另一种协议，为美国斯坦福大学在1986年开始研发的多重邮箱电子邮件系统。它能够从邮件服务器上获取有关E－mail的信息或直接收取邮件，具有高性能和可扩展性的优点。IMAP为很多客户端电子邮件软件所采纳，如OutlookExpress、NetscapeMessenger等，支持IMAP的服务器端的软件也越来越多，如CriticalPath、Eudora、iPlanet、Sendmail等。
<br />
### 端口
* Port 143 - this is the default IMAP non-encrypted port
* Port 993 - this is the port you need to use if you want to connect using IMAP securely

## MIME
MIME(Multipurpose Internet Mail Extensions)，即多用途Internet邮件扩展标准。它不是邮件传输协议，而是对传输内容的消息、附件及其它的内容定义了格式。这里有很多不同的RFC(Requirement of Comment)文档：RFC 822、RFC 2045、RFC 2046 和 RFC 2047。 

## 参考
* [Mail的一些基本概念总结](blogread.cn/it/article/5906?f=wb1)
* [Email Protocols - POP3, SMTP and IMAP](https://www.siteground.com/tutorials/email/pop3-imap-smtp-ports.htm)

<br />
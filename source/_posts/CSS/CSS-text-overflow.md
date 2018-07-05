---
title: 'CSS:text-overflow'
categories: C-CSS
permalink: 'cs:text-overflow'
translate_title: 'css:text-overflow'
date: 2016-08-27 10:43:52
tags:
description:
---
## 概述
读了一篇CSS文章[CSS实现单行、多行文本溢出显示省略号](http://www.w3cfuns.com/notes/17492/af1a09a544260e22a07144436026e2c4.html)，发现对CSS的`overflow`、`text-overflow`属性掌握的理解掌握的并不是很牢靠，所以找了篇文章巩固下。 


## text-overflow属性
CSS中的`text-overflow`属性用于解决当文字内容超出包含块的大小时被裁减的情况。使用该属性，文字可能被裁减、显示为省略号或显示为用户定义的字符。   

使用方法如下:  

```
.ellipsis{
	text-overflow: ellipsis;
	
	/* text-overflow属性必须依赖下面两个属性 */
	white-space: nowrap;
	overflow: hidden;
}
```

> 注意: text-overflow属性只有在包含块的overflow属性为值: hidden、scroll、auto且white-space属性值为nowrap时才生效。  

文本溢出仅仅对**block**或**inline-block**元素有效，因为需要对元素设置一个宽度。


## 使用示例

```
.clip {
    text-overflow: clip;

    overflow: hidden;
    white-space: nowrap;
}

.ellipsis {
    text-overflow: ellipsis;

    overflow: hidden;
    white-space: nowrap;
}

.word {
    text-overflow: ellipsis-word;

    overflow: hidden;
    white-space: nowrap;
}

.text {
    text-overflow: "===";

    overflow: hidden;
    white-space: nowrap;
}
```

设置**overflow**属性值为**auto**或**scroll**可以通过拖动滚动条查看被隐藏的文字，但是**hidden**属性不会。可以通过选择**..**选择被隐藏的文字。  


## JS实现
使用text-overflow属性貌似只能实现单行文本的溢出，对于多行文本的溢出，本文简介提到的文章中使用了一些技巧，但是可能会有浏览器兼容问题，具体有没有尚未研究。  

在文章的评论里发现了一个专门实现文本溢出的JQuery插件,[jquery.ellipsis.js](https://github.com/jjenzz/jquery.ellipsis)


## 总结
这次应该算是正式切入前端了，目标是一边看前端的基础知识，一边深入研究各个知识点。争取把以前做PHP涉及到的知识点给挨个补全了。  

本篇文章参考了**css-tricks[https://css-tricks.com]**网站的文章，该网站包含几乎所有的CSS属性的详细解释，所以本篇文章也算是对该网站知识点学习的一个起步。  


## 参考
* [text-overflow](https://css-tricks.com/almanac/properties/t/text-overflow/)
* [CSS实现单行、多行文本溢出显示省略号](http://www.w3cfuns.com/notes/17492/af1a09a544260e22a07144436026e2c4.html)

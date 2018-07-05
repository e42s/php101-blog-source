---
title: 'CSS:内联元素和块级元素的区别(译)'
categories: C-CSS
permalink: 'cs:-the-diference-betwen-inline-and-block-level-elements'
translate_title: 'the-difference-between-the-css:-inline-element-and-the-block---level-element'
date: 2016-03-23 16:25:07
tags:
description:
---
## 概述
在CSS盒模型中，HTML元素可以被分为几个不同的类型。这这些类型中，有两个最常见的类型：块级(block-level)元素和内联(Inline)元素。  

作为一名后端程序员，经常会接触到这些概念。以前也仅仅知道，块级元素和内联元素的简单区别。现在的工作中已经离不开前端了，所以想深入了解下块级元素和内联元素的区别，为之后的前端学习多打一点基础。  


## 块级元素
块级元素具有以下特点:

* 如果未设置宽度，宽度会自动时应到它父级元素的宽度。
* 如果未设置高度，高度默认为其子元素的高度(前提是子元素没有设置浮动和position定位)。
* 可以设置margin/padding。
* 默认的，块级元素独占一行。
* 会忽略vertical-align属性。

因此，如果我们想让块级元素自动适应父元素的宽度，就不要给块级元素设置宽度或者设置一个*100%*的宽度，这样反而不好维护。  

HTML中常见的块级元素:
`<p> `,`<div> `,`<form>`,`<header>`,`<nav>`,`<ul>`,`<li>`,`<h1>`


## 内联元素
内联元素通常有以下特点:

* 内联元素并不独占一行
* 可以设置white-space属性
* 内联元素会忽略margin-top和margin-bottom,但是可以设置margin-left和margin-right，同事可以设置四个方向的padding
* 内联元素会忽略宽度和高度属性
* 内联元素一旦浮动，就会自动变为块级元素，并且拥有块级元素的所有属性
* 可以设置vertical-align属性


HTML中常见的内联元素:
`<a>`,`<span>`,`<b>`,`<em>`,`<i>`,`<cite>`,`<mark>`,`<code>`


## 注意事项
如前面所说，我们可以很清晰的分辨块级元素和内联元素：*块级元素是结构型元素,内联元素是基于文本的*，这是一种很容易区别块级元素和内联元素的方式。  

一般来讲，我们可以将任何块级元素放入嵌套在一个块级元素中，也可以将任何内联元素嵌套在块级元素中或者将内联元素嵌套在内联元素中，*但是不能将块级元素放在内联元素中，有一个例外的元素是`<a>`，可以将任何元素放置在`<a>`元素中*。  

我们可以使用`<display>`属性将块级元素和内联元素进行转换。  

有一点需要注意的是，从CSS的角度来说，块级元素和内联元素是存在的，但是在HTML5，不同类型的元素的已经被重新分类。具体的可以参考如下HTML5文档：[content models](https://html.spec.whatwg.org/multipage/dom.html#content-models)。

## 小贴士：替换元素(Replaced Elements)
在文章开头提到，块级元素和内联元素是两种基本类型。总得来说，它们仅仅是基本类型中的两种，而且我们不必要为其他类型的元素而担心。  

还有一种特别的元素叫，**替换元素（Replaced Elements）**。它既不属于块级元素也不属于内联元素。关于替换元素的详细内容，可以参考这篇文章:[Replaced Elements](http://reference.sitepoint.com/css/replacedelements)。  

## 总结
深入理解块级元素和内联元素能帮助我们更好的使用CSS，避免有时候滥用一些属性。  

## 参考
* [The Difference Between “Block” and “Inline”
](http://www.impressivewebs.com/difference-block-inline-css/)

<br />


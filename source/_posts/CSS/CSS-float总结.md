---
title: 'CSS:float总结'
categories: C-CSS
permalink: 'cs:float-sumary'
translate_title: 'css:float-summary'
date: 2016-08-29 02:02:03
tags:
description:
---
## 概述
本篇文章是在搜索了大量关于CSS中float属性相关的文章之后，做的一个总结和整理。

## Float的历史
Float设计的初衷，是为了实现文字环绕效果。嗯，就这么简单。

## 包含块
浮动元素的包含块就是离浮动元素**最近的块级祖先元素**，前面叙述的例子中，div.box就是span元素的包含块。


## 浮动元素特性
* 只有横向浮动，并没有纵向浮动。
* 当元素应用了float属性后，将会脱离普通流，其容器（父）元素将得不到脱离普通流的子元素高度。
* 会将元素的display属性变更为block。 
* 浮动元素的延伸性。我们可以将其理解为元素被设置成浮动后，该元素会进行延伸进而包含其所有浮动的子元素。
* 浮动元素的后一个元素会围绕着浮动元素（典型运用是文字围绕图片），与应用了position的元素相比浮动元素并不会遮盖后一个元素。
* 浮动元素的前一个元素不会受到任何影响（如果你想让两个块状元素并排显示，必须让两个块状元素都应用float)。


## float与position兼容问题
* 元素同时应用了position: relative、float、（top / left / bottom / right）属性后，则元素先浮动到相应的位置，然后再根据（top / left / bottom / right）所设置的距离来发生偏移。
* 元素同时应用了position: absolute及float属性，则float失效。
* 第一个元素应用了position(absolute)之后会覆盖着接下来的float元素（如果两个元素所处的位置相同）
* 同时应用position: absolute和float: left会导致清除浮动无效（position: relative则可以清除浮动）。


## Float引起的父元素高度塌陷BUG？
不，不是BUG。

子元素浮动引起父元素高度塌陷的原因如下：
**因为没有预先设置div高度，所以div高度由其包含的子元素高度决定。而浮动脱离文档流，所以图片并不会被计算高度。此时的div中，相当于div中子元素高度为0，所以发生了父元素高度塌陷现象。**

## 文字环绕效果的实现
那么，文字环绕效果是如何实现的？  

**其实讲起来也很简单，因为父元素高度塌陷，所以文字会按正常顺序排列，无视图片高度。而图片宽度又还在，所以实现了文字环绕效果。**




## 清除浮动
### 1.增加额外的div
```
<div style="clear:both"></div>
```
优点: 简单直接，初学者常常使用的方法，也易于理解
缺点：增加额外的无意义标签，不利于语义化，每次清除都要添加额外的空标签，造成浪费

**撑开父元素空间的奥秘**

```
“在 CSS2.1 中，会在设置清除浮动的元素外边距之上增加清除空间，而外边距本身并不改变。”
```
也就是说，因为浮动而产生的空白空间，会被填充为实际存在的空间。。那么，自然就能撑开父元素。

<br />
### 2.父级元素添加overflow:hidden
这个方法的关键在于触发了BFC，BFC是CSS中的难点之一，我们以后专门来学习这个概念。现在只需要知道它可以清除浮动带来的影响。  

```
.clearfix {
	overflow:hidden;
	zoom:1
}
```
优点：代码量少，没有额外的标签  
缺点：如果子元素超出父元素的范围，会造成超出的部分被隐藏

<br />
### 3.after伪元素
after表示子元素的后面，通过它可以设置一个具有clear的元素，然后将其隐藏  

```
.clearfix:{
    zoom:1
}
.clearfix:after{
    display:block;
    content:'';
    clear:both;
    height:0;
    visibility:hidden;
}
```
这种方法的原理和第一个方法一样，就是生成一个元素来清除浮动，只是这个元素是“假的”。  

优点：没有额外标签，综合起来算是比较好的方法  
缺点：稍显复杂，但是理解其原理后也挺简单的

## 浮动与清除浮动的顺序关系
1.设置元素A浮动，元素脱离文档流，不计算高度。父元素出现高度塌陷。
2.浮动元素A产生空白空间。空间长宽等于元素A的长宽。后面元素会自动补空缺。
3.给浮动元素之后的元素B设置`clear:both`，元素B将不去补空缺。
4.元素B不仅不补空缺，还会把元素A因浮动而产生的空白空间填充为实际空间。
5.实际空间被计算高度，父元素被撑开。

## float的应用
* 文字环绕效果
* 横向菜单排列
* 布局


##  参考
* [详解CSS float属性](http://luopq.com/2015/11/08/CSS-float/)
* [对CSS中的Position、Float属性的一些深入探讨](http://www.cnblogs.com/coffeedeveloper/p/3145790.html)
* [理解CSS浮动与清除浮动](https://segmentfault.com/a/1190000004422807)
* [浮动的一些探究](https://segmentfault.com/a/1190000004391587)
* [float是如何工作的](http://web.jobbole.com/83403/)
* [CSS的Float之二](http://www.w3cplus.com/css/css-containing-floats-part-2)

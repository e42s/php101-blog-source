---
title: javascript对象与JSON
categories: C-javascript基础
tags:
  - JSON
permalink: javascript-objects-and-json
translate_title: javascript-objects-and-json
date: 2014-08-12 16:59:15
---
## 简介
JSON即JavaScript Object Natation，它是一种轻量级的数据交换格式，非常适合于服务器与 JavaScript 的交互。  
JSON是一种数据交换格式，像XML和YAML一样是一种在各种不同语言间传递结构化信息的方式。从另一方面来说，javascript对象是javascript语言中的一种数据类型，就像PHP中的数组、C++中类和结构体。  
## 定义JSON与javascript对象
在javascript程序中定义对象的时候，对象的属性名*可以加双引号也可以不加双引号*。如果属性名包含特殊字符(如!、if等)的时候，就必须加双引号。
在定义JSON的时候，属性名必须加双引号。  
代码示例：  
1. 定义javascript对象

```
var obj={name:"tudouya","sex":"man"};  #两个属性可以加双引号也可以不加
var obj={"!":"hello world"};  #属性名包含特殊字符时必须加双引号
```
2.定义JSON字符串
```
var jsonString={"name":"tudouya"};  #定义JSON时必须加双引号
```
## javascript对象转换为JSON
1.javascript对象转换为JSON  
我们可以使用javascript的内置函数将javascript对象转换为JSON，这个函数就是JSON.stringify().  
代码示例：    

```
var obj={name:"tudouya",sex:"man"};
var jsonObj=JSON.stringify(obj);
console.log(jsonObj);

##输出结果为：{"name":"tudouya","sex":"man"}
```

在将javascript对象转换为JSON的时候，有一点是需要我们注意的：  
如果对象中包含有值为函数和日期的属性，JSON会忽略值为函数的属性，并将值为日期的属性转换为字符串。  
代码示例：  

```
var obj={
	name:"tudouya",
	birthday:new Date(),
	action:function (){
		document.write("walk");
	}
};
var jsonObj=JSON.stringify(obj);
console.log(jsonObj);
```

输出结果为：  

```
{"name":"tudouya","birthday":"2014-08-12T10:05:00.497Z"}
```
## javascript中解析JSON
在老版本的JS中，大家通常都使用eval()函数来解析JSON，但是ECMAScript5给我们提供了一个解析JSON的新函数JSON.parse()。
这个函数的使用方法比较简单，大家可以自行尝试。当对某个JSON字符串应用该函数以后，该JSON就被转换为javascript的对象，也就是说当用typeof运算符查看该函数的类型时，返回的值是Object。  
同样有一点需要注意的是，该函数是ECMAScript5以后才支持的，如果是旧版本的浏览器那么可能不支持该函数。解决的方法是加载一个实现该函数的js文件，即[json2.js](https://github.com/douglascrockford/JSON-js)。如果使用的是JQuery框架，jQuery.parseJSON()，该函数调用了JSON.parse()方法。  
关于使用eval()方法解析JSON，这个等深入学习后会记录下来。
## 一个很重要的概念
作为一个前端菜鸟，经常听到别人说“JSON对象”，但是其实并没有“JSON对象”这个概念，JSON真正的表现形式是字符串。
## 引用
1.[What is the difference between JSON and Object Literal Notation](http://stackoverflow.com/questions/2904131/what-is-the-difference-between-json-and-object-literal-notation)  
2.[Javascript object Vs JSON](http://stackoverflow.com/questions/8294088/javascript-object-vs-json)  
3.[There's no such thing as a "JSON Object"](http://benalman.com/news/2010/03/theres-no-such-thing-as-a-json/)

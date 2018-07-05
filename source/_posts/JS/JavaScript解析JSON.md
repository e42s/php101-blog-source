---
title: JavaScript解析JSON
categories: C-javascript基础
tags:
  - JSON
permalink: javascript-parsing-json
translate_title: javascript-parsing-json
date: 2014-09-27 18:15:46
description:
---
## 概述
现在JSON格式在web开发中越来越受重视,特别是在使用ajax开发项目的过程中,经常需要将json格式的字符串返回到前端，前端解析成JS对象（JSON ）。  
**ECMA-262(E3)**中没有将JSON概念写到标准中，还好在 ECMA-262(E5)中JSON的概念被正式引入了，包括全局的JSON对象和Date的toJSON方法。<br />
## 解析JSON数据的三种方法
### eval()方法
解析JSON数据的最常用方法是使用javascript的eval()方法，代码如下：
```
    function toJson(str){
    	var json = eval('(' + str + ')');
    	return json;
    }
```
该方法存在性能和安全方面的问题，不建议使用。
### new Function方法
```
function toJson(str){
	var json = (new Function("return " + str))();
	return json;
}
```
### JSON.parse()方法
这种方法只支持IE8/Firefox3.5+/Chrome4/Safari4/Opera10 以上版本,这些浏览器都已经接近W3C标准，默认实现了toJSON方法。
```
function toJson(str){
	return JSON.parse(str);
}
```
[json2.js](https://github.com/douglascrockford/JSON-js)会在浏览器原生支持JSON.parse的时候选用原生版本，而且它跟ES5是API兼容的。在ES5还没完全普及的现状下，John Resig大神推荐用json2.js主要是为了现在就能用跟ES5兼容的API，日后能平滑过渡到ES5——只要去掉一个import就换过来了。<br />
## 总结
本文介绍了解析JSON对象的三种方法，大家可以根据情况选择合适的方法。

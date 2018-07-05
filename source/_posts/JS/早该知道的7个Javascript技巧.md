---
title: 早该知道的7个Javascript技巧
categories: C-javascript基础
permalink: 7-javascript-techniques-that-should-have-ben-known
translate_title: the-7-javascript-techniques-that-should-have-been-known
date: 2014-10-08 09:51:36
tags:
description:
---
## 概述
恶补JS基础有一段时间了，虽然是一个前端的菜逼，但是一直挺关注一些编码最佳实践。看到这篇文章，所以转载过来了。记录下自己已经掌握了哪些最佳实践。
## 简洁写法(已掌握)
### 对象
JavaScript里我最喜欢的一种东西就是生成对象和数组的简写方法。通常，创建对象都使用下面的方法：
```
		var car=new Object();
    	car.color="red";
    	car.wheels=4;
    	car.age=5;
    	console.log(car);
```
下面的写法能实现同样的功能，
```
		var car={
    			color:'red',
    			wheels:4,
    			age:5
    	};
    	console.log(car);
```
这样写简单多了，我们不需要反复使用这个对象名称。也许你会遇到 invalidUserInSession 的问题，这只有你在使用IE时会碰到，只要记住一点，不要右大括号前面写逗号，你就不会有麻烦。  
### 数组
另外一个十分方便的简写是针对数组的。传统的定义数组的方法是这样：
```
    	var arr=new Array('china','America','Japan');
```
简写版的是这样：
```
    	var arr=['china','America','Japan'];
```
对于数组，这里有个问题需要注意，**JavaScript不像PHP，它是没有关联数组的概念的**。经常会看到有人使用下面的语法：
```
    	var car=new Array();
    	car['color']='red';
    	car['wheels']=4;
    	car['age']=5;
    	console.log(car);  //输出 []
```
这样写其实是不对的，容易让人混淆。JavaScript中一切皆为对象，上面那段代码理解起来为：首先声明了一个空数组，然后给数组赋值，相当于给对象增加了几个成员，这时候我们又不能通过索引的方式去获得该元素。**总结起来一句话，JavaScript中没有关联数组，JavaScript中对象的成员有两种访问方式，一种是通过.运算符，一种是通过[]方式，JavaScript中的关联数组其实就是对象。**
### 三元运算符
有下面一段程序：
```
    	var direction;
	 	if(x < 200){
	 		direction = 1;
	 	} else {
	 		direction = -1;
	 	}
```
可以使用三元运算符简化它：
```
var direction = x < 200 ? 1 : -1;
```
## 用 JSON 形式存储数据(未完全掌握)
Douglas Crockford 发明了[JSON](json.org)之后，一切全变了。使用JSON，你可以使用JavaScript自有功能把数据存贮成复杂的格式，而且不需要再做其它的额外转换，直接可以访问使用。JSON 是 “JavaScript Object Notation” 的缩写，它用到了上面提到的两种简写方法。于是，如果你想描述一个乐队，你可能会像这样写：
```
var band = {
  "name":"The Red Hot Chili Peppers",
  "members":[
    {
      "name":"Anthony Kiedis",
      "role":"lead vocals"
    },
    {
      "name":"Michael 'Flea' Balzary",
      "role":"bass guitar, trumpet, backing vocals"
    }, 
    {
      "name":"Chad Smith",
      "role":"drums,percussion"
    },
    {
      "name":"John Frusciante",
      "role":"Lead Guitar"
    }
  ],
  "year":"2009"
}
```
你可以在JavaScript里直接使用JSON，可以把它封装在函数里，甚至作为一个API的返回值形式。我们把这称作 JSON-P ，很多的API都使用这种形式。  
基本上，JSON是最轻便的描述复杂数据结构的方法，而且它能在浏览器里运行。你甚至可以在PHP里用 json_decode() 函数来运行它。
## 原生JavaScript函数(Math,Array,String)(未完全掌握)
JavaScript提供了很多原生函数，使用它们可以大大的简化我们的编程劳动。例如，当需要实现一个功能，找出数字数组里最大的一个数时，通常是使用一种算法，如下：
```
var numbers = [3,342,23,22,124];
var max = 0;
for(var i=0;i<numbers.length;i++){
  if(numbers[i] > max){
    max = numbers[i];
  }
}
alert(max);
```
不用循环也能实现：
```
var numbers = [3,342,23,22,124];
numbers.sort(function(a,b){return b - a});
alert(numbers[0]);
```
需要注意的是，不能对一个数字字符数组进行 sort() ，因为这种情况下它只会按照字母顺序进行排序。  
再有一个有意思的函数就是 Math.max()。这个函数返回参数里的数字里最大的一个数字。
## 事件委派(未掌握)
Web应用都是由事件驱动运转的。我喜欢事件处理，尤其喜欢自己定义事件。它能使你的产品可扩展，而不用改动核心代码。有一个很大的问题（也可以说是功能强大的表现），是关于页面上事件的移除问题。你可以对某个元素安装一个事件监听器，事件监听器就开始运转工作。但页面上没有任何指示说明这有个监听器。因为这种不可表现的问题 (这尤其让一些新手头疼) ，以及像IE6这样的”浏览器“在太多的使用事件监听时会出现各种的内存问题，你不得不承认尽量少使用事件编程是个明智的做法。  
于是 事件委托 就出现了。  
当页面上某个元素上的事件触发时，而在 DOM 继承关系上，这个元素的所有子元素也能接收到这个事件，这时你可以使用一个在父元素上的事件处理器来处理，而不是使用一堆的各个子元素上的事件监听器来处理。究竟是什么意思？这样说吧，页面上有很多超链接，你不想直接使用这些链接，想通过一个函数来调用这个链接，HTML代码是这样的：
```
<h2>Great Web resources</h2>
<ul id="resources">
  <li><a href="http://opera.com/wsc">Opera Web Standards Curriculum</a></li>
  <li><a href="http://sitepoint.com">Sitepoint</a></li>
  <li><a href="http://alistapart.com">A List Apart</a></li>
  <li><a href="http://yuiblog.com">YUI Blog</a></li>
  <li><a href="http://blameitonthevoices.com">Blame it on the voices</a></li>
  <li><a href="http://oddlyspecific.com">Oddly specific</a></li>
</ul>
```
常见的做法是通过循环这些链接，将每个链接上附加一个事件处理器：
```
(function(){
  var resources = document.getElementById('resources');
  var links = resources.getElementsByTagName('a');
  var all = links.length;
  for(var i=0;i<all;i++){
    // Attach a listener to each link
    links[i].addEventListener('click',handler,false);
  };
  function handler(e){
    var x = e.target; // Get the link that was clicked
    alert(x);
    e.preventDefault();
  };
})();
```
我们用一个事件处理器也能完成这项任务：
```
(function(){
  var resources = document.getElementById('resources');
  resources.addEventListener('click',handler,false);
  function handler(e){
    var x = e.target; // get the link tha
    if(x.nodeName.toLowerCase() === 'a'){
      alert('Event delegation:' + x);
      e.preventDefault();
    }
  };
})();
```
因为点击事件就发生在这些页面元素里，你要做的就是比较它们的 nodeName，找出应该回应这个事件的那个元素。
## 匿名函数和模块化(未完全掌握)
在JavaScript里最令人懊恼的事情是变量没有使用范围。**任何变量，函数，数组，对象，只要不在函数内部，都被认为是全局的**，这就是说，这个页面上的其它脚本也可以访问它，而且可以覆盖重写它。  

解决办法是，把你的变量放在一个匿名函数内部，定义完之后立即调用它。例如，下面的写法将会产生三个全局变量和两个全局函数：
```
var name = 'Chris';
var age = '34';
var status = 'single';
function createMember(){
  // [...]
}
function getMemberDetails(){
  // [...]
}
```
如果这个页面上的其它脚本里也存在一个叫 status 的变量，麻烦就会出现。如果我们把它们封装在一个 myApplication 里，这个问题就迎刃而解了
```
var myApplication = function(){
  var name = 'Chris';
  var age = '34';
  var status = 'single';
  function createMember(){
    // [...]
  }
  function getMemberDetails(){
    // [...]
  }
}();
```
但是，这样一来，在函数外面就没有什么功能了。如果这是你需要的，那就可以了。你还可以省去函数的名称：
```
(function(){
  var name = 'Chris';
  var age = '34';
  var status = 'single';
  function createMember(){
    // [...]
  }
  function getMemberDetails(){
    // [...]
  }
})();
```
如果你想在函数外面也能使用里面的东西，那就要做些修改。为了能访问 createMember() 或 getMemberDetails(),你需要把它们变成 myApplication的属性，从而把它们暴露于外部的世界:
```
var myApplication = function(){
  var name = 'Chris';
  var age = '34';
  var status = 'single';
  return{
    createMember:function(){
      // [...]
    },
    getMemberDetails:function(){
      // [...]
    }
  }
}();
// myApplication.createMember() and 
// myApplication.getMemberDetails() now works.
```
这被称作 module 模式或 singleton。Douglas Crockford 多次谈到过这些，Yahoo User Interface Library YUI 里对此有大量的使用。但这样一来让我感到不便的是，我需要改变句式来使函数和变量能被外界访问。更甚者，调用时我还需要加上myApplication 这个前缀。所以，我不喜欢这样做，我更愿意简单的把需要能被外界访问的元素的指针导出来。这样做后，反倒简化了外界调用的写法：
```
var myApplication = function(){
  var name = 'Chris';
  var age = '34';
  var status = 'single';
  function createMember(){
    // [...]
  }
  function getMemberDetails(){
    // [...]
  }
  return{
    create:createMember,
    get:getMemberDetails
  }
}();
//myApplication.get() and myApplication.create() now work.
```
我把这个称作 “revealing module pattern.”
## 可配置化
一旦我把所写的JavaScript代码发布到这个世界上，就有人想改动它，通常是人们想让它完成一些它本身完成不了的任务—但通常也是我写的程序不够灵活，没有提供用户可自定义的功能。解决办法是给你的脚本增加一个配置项对象。我曾经写过一篇深入介绍JavaScript配置项对象的文章，下面是其中的要点：
* 在你的脚本了添加一个叫做 configuration 的对象
* 这个对象里面，存放所有人们在使用这个脚本时经常要改动的东西  
    CSS ID 和类名称;  
    按钮的名称，标签字等;  
    诸如”每页显示图片数”的值， “图像的显示的尺寸”的值;  
    地点，位置，以及语言设置。  
* 将这个对象作为一个公用属性返回给用户，这样用户可以修改覆盖它  

通常情况下这是你编程过程中的最后一步要做的事情。我把这些集中表现在了一个例子里： “[Five things to do to a script before handing it over to the next developer.](http://www.wait-till-i.com/2008/02/07/five-things-to-do-to-a-script-before-handing-it-over-to-the-next-developer/)”。  
实际上，你也希望你的代码能够让人们很方面的使用，并且根据他们各自的需要进行一些改动。如果你实现了这个功能，你会少收到一些抱怨你的脚本的人发送给你的让你困惑的邮件，这些信件会告诉你，有人修改了你的脚本，而且很好用。
## 与后台交互
在这么多年的编程经历中，我所领悟到的一个重要的事情就是，JavaScript是一个很优秀的开发界面交互的语言，但如果用来处理数字或访问数据源，那就有点使不上劲了。  

最初，我学习JavaScript，是用来替代Perl的，因为我很讨厌非要把代码拷贝到 cgi-bin 文件夹下才能使Perl运行。后来，我明白了应该使用一种后台工作的语言来处理主要的数据，而不能什么事情都让JavaScript去做。更重要的是我们要考虑安全性和语言特征。  

如果我访问一个Web service, 我可以获取到JSON-P 格式的数据，在客户端浏览器里我把它做各种各样的数据转换，但当我有了服务器时，我有了更多的方法来转换数据，我可以在Server端生成JSON或HTML格式的数据返回给客户端，以及缓存数据等操作。如果你事先了解了并准备了这些，你会长期收益，省去了很多头疼的时间。编写适用各种浏览器的程序是种浪费时间，使用工具包吧！  

在我最初开始搞Web开发时，在访问页面时，究竟是使用 document.all 还是使用 document.layers 的问题上痛苦的挣扎了很久。我选择了 document.layers，因为我喜欢任何层都是自己的document的思想 (而且我写了太多的 document.write 来生成元素)。层模式最终失败了，于是我开始使用 document.all。当Netscape 6 公布只支持 W3C DOM 模型时，我很高兴，但其实用户并不关心这些。用户只是看见这种浏览器不能显示大多数浏览器都能正常显示的东西—这是我们编码的问题。我们编写了短视的代码，只能运行在当前的环境下，而不幸的是，我们的运行环境却在不停的改变。  

我已经浪费了太多的时间来处理对各种浏览器各种版本兼容的问题。善于处理这类问题提供了我一个好的工作机会。但现在我们不必在忍受这种痛苦了。  

一些工具包，例如 YUI, jQuery 以及Dojo 都能够帮我们处理这类问题。它们通过抽象各种接口实现来处理浏览器的各种问题，像版本不兼容，设计缺陷等，把我们从痛苦中解救出来。除非你要测试某个Beta版的浏览器，千万不要在自己的程序里添加修正浏览器的缺陷的代码，因为你很有可能当浏览器已经修改了这个问题时，你却忘了删除你的代码。  

另一方面，完全依赖于工具包也是个短视的行为。工具包可以帮你快速的开发，但如果你不深入理解JavaScript，你也会做错事。

##  原文
英文原文：[Seven JavaScript Things I Wish I Knew Much Earlier In My Career](http://www.smashingmagazine.com/2010/04/20/seven-javascript-things-i-wish-i-knew-much-earlier-in-my-career/)



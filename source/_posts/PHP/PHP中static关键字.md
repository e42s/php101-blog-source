---
title: PHP中static关键字
categories: A-PHP中高级
permalink: static-keyword-in-php
translate_title: the-static-keyword-in-php
date: 2015-06-23 16:19:26
tags:
---
## 概述
正在学习设计模式，之前有一篇文章关于[单例模式](http://www.php101.cn/2014/08/15/PHP%E5%8D%95%E4%BE%8B%E6%A8%A1%E5%BC%8F/)的文章，重新读了这篇文章，发现对`static`关键字掌握不是很牢靠，重新温习一下。

## static关键字
PHP手册里对`static`关键字的介绍如下：
```
Declaring class properties or methods as static makes them accessible without needing an instantiation of the class. A property declared as static cannot be accessed with an instantiated class object (though a static method can).
```
大体意思是，将类的属性和方法声明为**静态**以后，可以直接访问静态属性和方法，而不需要实例化对象。  

PHP中静态成员和方法的特性如下：
* 静态成员不能通过类的实例访问，但是静态方法可以。
* 静态成员不能通过`->`运算符访问。
* 在静态方法的作用域中，不能出现`$this`关键字，也就是说不能在静态方法中访问普通的成员变量。
* 静态成员和方法，都可以通过类名直接访问，而无需实例化对象。


<br />
## 迟绑定(Late Static Bindings)
下面的内容摘自PHP手册：
> 自 PHP 5.3.0 起，PHP 增加了一个叫做后期静态绑定的功能，用于在继承范围内引用静态调用的类。
> 
> 准确说，后期静态绑定工作原理是存储了在上一个“非转发调用”（non-forwarding call）的类名。当进行静态方法调用时，该类名即为明确指定的那个（通常在 :: 运算符左侧部分）；当进行非静态方法调用时，即为该对象所属的类。所谓的“转发调用”（forwarding call）指的是通过以下几种方式进行的静态调用：self::，parent::，static:: 以及 forward_static_call()。可用 get_called_class() 函数来得到被调用的方法所在的类名，static:: 则指出了其范围。


对该特性的理解，可以参考下手册中的[例子](http://php.net/manual/zh/language.oop5.late-static-bindings.php)

## self vs static
用一个demo来直接说明self与static的区别。

### self示例：
```
<?php
class Vehicle {
    protected static $name = 'This is a Vehicle';

    public static function what_vehicle() {
        echo get_called_class()."\n";                
        echo self::$name;
    }
}

class Sedan extends Vehicle {
    protected static $name = 'This is a Sedan';
}

Sedan::what_vehicle();
```
程序输出:
```
Sedan
This is a Vehicle 
```
<br />
### static示例：
```
<?php
class Vehicle {
    protected static $name = 'This is a Vehicle';

    public static function what_vehicle() {
        echo get_called_class()."\n";        
        echo static::$name;
    }
}

class Sedan extends Vehicle {
    protected static $name = 'This is a Sedan';
}

Sedan::what_vehicle();
```
程序输出：
```
Sedan
This is a Sedan
```
<br />

## 总结
看看上一篇文章，已经一个多月没更新过博客了，忙是一部分，主要的还是自己懈怠了，以后还得坚持。这篇文章写的也有点没感觉。

## 参考
* [Learning About Late Static Bindings in PHP](http://infopotato.com/blog/index/late_static_bindings)
* [后期静态绑定](http://php.net/manual/zh/language.oop5.late-static-bindings.php)
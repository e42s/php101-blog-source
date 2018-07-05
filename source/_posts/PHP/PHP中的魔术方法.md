---
title: PHP中的魔术方法
categories: A-PHP中高级
permalink: magic-methods-in-php
translate_title: magic-methods-in-php
date: 2015-01-29 21:35:45
tags:
description:
---
## 概述
在面向对象编程中，PHP提供了一系列的魔术方法，这些魔术方法为编程提供了很多便利。PHP中的魔术方法通常以__(两个下划线)开始，并且不需要显示的调用而是由某种特定的条件出发。这篇文章简单总结了PHP中提供的魔术方法。


## 开始之前
在总结PHP的魔术方法之前先来定义两个类，以便后边示例使用：
```
<?php
class Device {
    public $name;           
    public $battery;        
    public $data = array(); 
    public $connection;     
 
    protected function connect() {
        $this->connection = 'resource';
        echo $this->name . ' connected' . PHP_EOL;
    }
 
    protected function disconnect() {
        $this->connection = null;
        echo $this->name . ' disconnected' . PHP_EOL;
    }
}
 
class Battery {
    private $charge = 0;
 
    public function setCharge($charge) {
        $charge = (int)$charge;
        if($charge < 0) {
            $charge = 0;
        }
        elseif($charge > 100) {
            $charge = 100;
        }
        $this->charge = $charge;
    }
}
?>
```
Device类有四个成员属性和两个成员方法。Battery类有一个成员属性和一个成员方法。

## 构造函数和析构函数
构造函数和析构函数分别在对象创建和销毁时被调用。对象被“销毁”是指不存在任何对该对象的引用，比如引用该对象的变量被删除(unset)、重新赋值或脚本执行结束，都会调用析构函数。

### \_\_construct()
\_\_construct()构造函数是目前为止最经常使用的函数。在创建对象时，可以在构造函数中做一些初始化工作。可以为构造函数定义任意多个参数，只要在实例化时传入对应个数的参数即可。构造函数中出现的任何异常都会阻止对象的创建。
```
class Device {
   public function  __construct(Battery $battery, $name) {
       $this->battery = $battery;
       $this->name = $name;
       $this->connect();
    }
}
```
上面的示例代码中，Device类的构造函数为成员属性赋值并且调用了connect()方法。
> 将构造函数声明为私有方法，可以防止在类外部创建对象，这在单利模式中经常使用。

<br />
### \_\_desctruct()
析构函数通常在对象被销毁时调用，析构函数不接收任何参数。经常在析构函数中执行一些清理工作，比如关闭数据库连接等。
<br />
## 属性重载(Property Overloading)
> 有一点需要注意的是：PHP中的”重载"与其他大多数语言的重载不是太一样，虽然都实现了相同的功能。

属性重载涉及到的两个魔术方法主要是用来处理属性访问，定义了当我们尝试访问一个不存在(**或不可访问**)的属性时会发生什么。
<br />
### \_\_get()
魔术方法\_\_get()在我们尝试访问一个不存在的属性时会被调用。它接收一个参数，该参数表示访问属性的名字，并且将该属性的值返回。在上面的Device类里，有一个data属性，该属性就在这里就起了作用，如下面得代码：
```
class Device {
    public function  __get($name) {
         if(array_key_exists($name, $this->data)) {
            return $this->data[$name];
        }
        return null;
    }
}
```
该魔术方法最常用的地方就是通过创建一个“只读”的属性来扩展访问控制。在上面的Battery类中，有一个私有属性`$charge `，我们可以通过\_\_get()魔术方法将该属性扩展为在类外部可读但不能修改。代码如下：
```
class Battery {
    private $charge = 0;
 
    public function  __get($name) {
        if(isset($this->$name)) {
            return $this->$name;
        }
        return null;
    }
}
```
<br />
### \_\_set()
\_\_set()魔术方法在我们尝试修改一个不可访问的属性时会被调用，它接收两个参数，一个表示属性的名字，一个表示属性的值。示例代码如下：
```
class Device {
    public function  __set($name, $value) {
        // use the property name as the array key
        $this->data[$name] = $value;
    }
}
```
<br />
### \_\_isset()
\_\_isset()魔术方法在对一个不可访问的属性调用isset()方法时会被调用，它接收一个参数，表示属性的名字。它应该返回一个布尔值，用来表示该属性是否存在。代码如下：
```
class Device {
    public function  __isset($name) {
        return array_key_exists($name, $this->data);
    }
}
```
<br />
### \_\_unset()
\_\_unset()魔术方法在调用unset()函数销毁一个不能访问的属性时会被调用，它接收一个参数，表述属性的名字。

<br />
## 对象转换为字符串
有时候我们需要将对象以字符串的形式表现出来。如果我们直接打印一个对象，那么程序将会输出一个错误信息：`PHP Catchable fatal error:  Object of class Device could not be converted to string `
<br />
### \_\_toString()
\_\_toString()在我们将对象当作字符串一样使用时会被调用，它不接收任何参数。该方法允许我们定义对象的表现形式。代码如下：
```
class Device {
    public function  __toString() {
       $connected = (isset($this->connection)) ? 'connected' : 'disconnected';
       $count = count($this->data);
       return $this->name . ' is ' . $connected . ' with ' . $count . ' items in memory' . PHP_EOL;
    }
    ...
}
```
<br />
### \_\_set_state()(PHP 5.1)
**静态**魔术方法\_\_set_state()，在我们使用var_export()函数输出对象时会调用该方法。var_export()函数用来将PHP变量转换为PHP代码，它接收一个包含对象属性值的关联数组作为参数。示例代码如下：
```
class Battery {
    //...
    public static function  __set_state(array $array) {
        $obj = new self();
        $obj->setCharge($array['charge']);
        return $obj;
    }
    //...
}
```

<br />
## 克隆对象
默认的，对象都是按引用传值的。因此，在将一个对象赋值给另一个变量时，只是创建了指向该对象的一个引用，并没有复制该对象。为了实现真正得复制一个对象，我们需要使用`clone`关键字。

这种“按引用传递”的策略同样适用于包含在对象内部的对象。即使我们克隆了一个对象，在对象内部的任何对象都不会被克隆，因此最终的结果是两个对象共享了同一个内部对象。示例代码如下：
```
$device = new Device(new Battery(), 'iMagic');
$device2 = clone $device;
 
$device->battery->setCharge(65);
echo $device2->battery->charge;
// 65
```
<br />
### \_\_clone()
\_\_clone()魔术方法\_\_clone()可以解决上面的问题。当对一个对象使用clone关键字时，该魔术方法会被调用。在这个魔术方法里，我们可以实现任何子对象的克隆，代码如下：
```
class Device {
    ...
    public function  __clone() {
        // copy our Battery object
        $this->battery = clone $this->battery;
    }
    ...
}
```
<br />
## 对象序列化
序列化是讲任意数据转换为字符串格式的过程。序列化通常用来将整个对象存入数据库或写入文件中。当反序列化存储的数据时，我们可以得到序列化之前的对象。但是，并不是所有得数据都可以被序列化，比如数据库连接。幸运的是，有一个魔术方法可以帮我们解决这个问题。
<br />
### \_\_sleep()
魔术方法\_\_sleep()在对一个对象序列化时(调用serialize())会被调用。它不接收任何参数，而且应该返回一个包含所有应该被序列化的属性的数组。在该魔术方法中，也可以执行一些其他操作。

> 有一点要注意的是，不要再该函数中进行任何的析构操作，因为这可能会影响正在运行的对象。

示例代码如下：
```
class Device {
    public $name;           
    public $battery;       
    public $data = array();
    public $connection;    
    //...
    public function  __sleep() {
        return array('name', 'battery', 'data');
    }
    //...
}
```
<br />
### \_\_wakeup()
魔术方法\_\_wakeup()在对存储的对象反序列化时会被调用。它不接收任何参数，也没有任何返回值。可以用它来处理在序列化时丢失的数据库连接或资源。代码如下：
```
class Device {
    //...
    public function  __wakeup() {
        // reconnect to the network
        $this->connect();
    }
    //...
}
```
<br />
## 方法重载
PHP还有两个与成员方法相关的魔术方法\_\_call()和\_\_callStatic()，这两个魔术方法类似于属性重载方法。
<br />
### \_\_call()
魔术方法\_\_call()在调用不存在或不可访问的方法时会被调用。它接收两个参数，一个是调用的方法的名字，一个是包含函数参数的数组。我们可以使用这种方法调用子对象中得同名函数。  

> 在这个例子中，要注意函数`call_user_func_array()`，这个函数允许我们动态调用一个命名的函数。

示例代码如下:
```
class Device {
    //...
    public function  __call($name, $arguments) {
        // make sure our child object has this method
        if(method_exists($this->connection, $name)) {
            // forward the call to our child object
            return call_user_func_array(array($this->connection, $name), $arguments);
        }
        return null;
    }
    //...
}
```
<br />
### \_\_callStatic()
魔术方法\_\_callStatic()与\_\_call()的功能一样，唯一不同的是，该方法在尝试访问一个不存在或不可访问的静态方法时会被调用。示例代码如下：
```
class Device {
    //...
    public static function  __callStatic($name, $arguments) {
        // make sure our class has this method
        if(method_exists('Connection', $name)) {
            // forward the static call to our class
            return call_user_func_array(array('Connection', $name), $arguments);
        }
        return null;
    }
    //...
}
```
<br />
## 将对象作为函数
有时候我们会需要将对象作为函数使用。将对象作为函数使用，就像我们使用其他普通的函数一样，可以传参。
<br />
### \_\_invoke()(PHP 5.3)
魔术方法__invoke()在尝试将对象作为函数使用时会被调用。在该方法中定义的任何参数，都将被作为函数的参数。示例代码如下：
```
class Device {
    //...
    public function __invoke($data) {
        echo $data;
    }
    //...
}
$device = new Device(new Battery(), 'iMagic');
$device('test');
// equiv to $device->__invoke('test')
// Outputs: test
```
<br />
## 其他:\_\_autoload()
\_\_autoload()方法并不是一个魔术方法，但是这个方法非常有用。但是，对着PHP版本的更新，该函数已经不建议使用，取而代之的是`spl_auto_register()`函数。
<br />

## 参考
* [PHP官方文档](http://www.php.net/manual/language.oop5.magic.php)
* [Deciphering Magic Methods in PHP](http://code.tutsplus.com/tutorials/deciphering-magic-methods-in-php--net-13085)
<br />













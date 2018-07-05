---
title: Python中的Logging模块简介
categories: F-Python
tags: logging
permalink: brief-introduction-of-loging-module-in-python
translate_title: a-brief-introduction-to-the-logging-module-in-python
date: 2015-03-05 15:59:25
description:
---
## 概述
Python标准库中自带了强大的log库。许多程序员都喜欢用Print语句来debug程序（这其中当然也包括我这个PHP程序员）,但是,我们也可以使用log来调试程序。使用log来调试程序的一个好处是，当我们编写完程序的时候，不用挨个删除代码中得print语句。在这篇文章中，主要会涉及到Python中log的以下几个主题：

* 创建一个简单的Logger
* 如何为多个模块记录log
* log格式化
* log配置

## 创建一个简单得Logger
使用Python的logging模块创建一个logger是非常简单和直接的。为了便于理解，下面是一段示例代码以及该段代码的解释：
```
import logging
 
# add filemode="w" to overwrite
logging.basicConfig(filename="sample.log", level=logging.INFO)
 
logging.debug("This is a debug message")
logging.info("Informational message")
logging.error("An error has happened!")
```
* import logging，如使用Python的其他模块一样，要使用logging模块，我们首先需要导入该模块。
* logging.basicConfig(filename="sample.log", level=logging.INFO)，创建一个logger的最简单的方法是使用logging模块的baseConfig()方法，并传递给该函数几个**关键字参数**。它接收以下几个关键字参数：level,filename,filemode,format,datefmt和stream。在上面的例子中，我们传递了一个filename参数和一个level参数，并将levle设置为INFO级别。logging模块的日志级别分为**五种**：DEBUG,INFO,WARNING,ERROR,CRITICAL.
* 默认的，如果我们多次运行该程序，那么输出的日志结果会追加在文件末尾，如果想实现日志的覆盖，可以将filemode参数设置为`w`。像代码中`# add filemode="w" to overwrite`该句的示例一样。

运行该程序，程序的输出结果为：
```
INFO:root:Informational message
ERROR:root:An error has happened!
```
> 需要注意的是，代码中的debug信息并没有在结果中输出，这是因为我们把日志的级别设置为了`INFO`,所以只有高于该级别的日志才会被输出。  
> 输出结果中得`root`部分是指该日志信息来源于`root`logger或`main`logger。在本文的下面部分会对这里做介绍。
> 如果不使用baseConfig()方法，日志信息就会被输出到控制台或标准输出。

logging模块还可以用来将异常记录到文件或者任何配置文件中指定的地方。下面是一段示例代码：
```
import logging
 
logging.basicConfig(filename="sample.log", level=logging.INFO)
log = logging.getLogger("ex")
 
try:
    raise RuntimeError
except Exception, err:
    log.exception("Error!")
```
上面的示例代码在调试的时候非常有用。

## 为多个模块记录log(格式化)
在项目中，我们通常会有很多个模块，如果我们想为不同得模块记录log，并且将不同模块的log记录到同一个文件中，可以像下面得示例代码一样来实现。下面的代码首先会介绍一种简单的方式，然后会使用一种复杂但是灵活性更强的方法来实现。  
简单得实现方式：
```
import logging
import otherMod
 
#----------------------------------------------------------------------
def main():
    """
    The main entry point of the application
    """
    logging.basicConfig(filename="mySnake.log", level=logging.INFO)
    logging.info("Program started")
    result = otherMod.add(7, 8)
    logging.info("Done!")
 
if __name__ == "__main__":
    main()
```
在上面的代码中，我们导入了logging模块和自定义的otherMod模块，otherMod模块代码如下：
```
# otherMod.py
import logging
 
#----------------------------------------------------------------------
def add(x, y):
    """"""
    logging.info("added %s and %s to get %s" % (x, y, x+y))
    return x+y
```
运行上面的代码，可以在日志文件中发现如下内容：
```
INFO:root:Program started
INFO:root:added 7 and 8 to get 15
INFO:root:Done!
```
大家可能会从上面的内容中发现一些问题，在上面的三条日志信息中，我们不能很容易的发现每条日志信息都是从哪里来的。因此，我们需要修改上面的代码。下面是修改过后的代码：
```
import logging
import otherMod2
 
#----------------------------------------------------------------------
def main():
    """
    The main entry point of the application
    """
    logger = logging.getLogger("exampleApp")
    logger.setLevel(logging.INFO)
 
    # create the logging file handler
    fh = logging.FileHandler("new_snake.log")
 
    formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
    fh.setFormatter(formatter)
 
    # add handler to logger object
    logger.addHandler(fh)
 
    logger.info("Program started")
    result = otherMod2.add(7, 8)
    logger.info("Done!")
 
if __name__ == "__main__":
    main()
```
这里，我们创建了一个名为“exampleApp”logger实例。我们设置日志文件级别，日志文件句柄对象和日志格式对象。文件句柄必须将格式化对象设置为他的格式，同时文件句柄也必须被添加到logger实例对象中。
模块otherMod2的代码如下：
```
otherMod2.py
import logging
 
module_logger = logging.getLogger("exampleApp.otherMod2")
 
#----------------------------------------------------------------------
def add(x, y):
    """"""
    logger = logging.getLogger("exampleApp.otherMod2.add")
    logger.info("added %s and %s to get %s" % (x, y, x+y))
    return x+y
```
修改后的程序运行结果如下：
```
2012-08-02 15:37:40,592 - exampleApp - INFO - Program started
2012-08-02 15:37:40,592 - exampleApp.otherMod2.add - INFO - added 7 and 8 to get 15
2012-08-02 15:37:40,592 - exampleApp - INFO - Done!
```
从结果可以看出，日志信息中得`root`已经被删除了。相对的，它使用了我们的格式化输出，输出了友好的时间显示、logger名称、日志级别和日志信息。这些实际上是LogRecord的属性。要查看LogRecord的全部属性，可以在[官方文档](https://docs.python.org/2/library/logging.html#logrecord-attributes)中查看。

## logging模块配置
可以通过**三种方式**配置logging模块：通过函数(loggers、formatters、handlers)配置，如本篇文章中得示例代码；通过配置文件配置，并将其传给fileConfig()函数；通过创建包含配置信息的字典，并将其传给dictConfig()函数。
### 通过配置文件配置
配置文件示例：
```
[loggers]
keys=root,exampleApp
 
[handlers]
keys=fileHandler, consoleHandler
 
[formatters]
keys=myFormatter
 
[logger_root]
level=CRITICAL
handlers=consoleHandler
 
[logger_exampleApp]
level=INFO
handlers=fileHandler
qualname=exampleApp
 
[handler_consoleHandler]
class=StreamHandler
level=DEBUG
formatter=myFormatter
args=(sys.stdout,)
 
[handler_fileHandler]
class=FileHandler
formatter=myFormatter
args=("config.log",)
 
[formatter_myFormatter]
format=%(asctime)s - %(name)s - %(levelname)s - %(message)s
datefmt=
```
在配置文件中包含了两个logger：root和exampleApp。无论出于什么原因，“root”都是必须的。如果配置文件中未包含“root”logger，config.py中的_install_loggers函数会抛出一个ValueError类型得错误。  

下面是如何在Python程序中载入配置文件的示例：
```
# log_with_config.py
import logging
import logging.config
import otherMod2
 
#----------------------------------------------------------------------
def main():
    """
    Based on http://docs.python.org/howto/logging.html#configuring-logging
    """
    logging.config.fileConfig('logging.conf')
    logger = logging.getLogger("exampleApp")
 
    logger.info("Program started")
    result = otherMod2.add(7, 8)
    logger.info("Done!")
 
if __name__ == "__main__":
    main()
```
程序输出结果如下：
```
2012-08-02 18:23:33,338 - exampleApp - INFO - Program started
2012-08-02 18:23:33,338 - exampleApp.otherMod2.add - INFO - added 7 and 8 to get 15
2012-08-02 18:23:33,338 - exampleApp - INFO - Done!
```
<br />
### 通过字典配置
通过字典来配置logging模块是Python 2.7 以后的版本新增的功能，因此使用此种方法时一定要注意Python的版本。
示例代码：
```
# log_with_config.py
import logging
import logging.config
import otherMod2
 
#----------------------------------------------------------------------
def main():
    """
    Based on http://docs.python.org/howto/logging.html#configuring-logging
    """
    dictLogConfig = {
        "version":1,
        "handlers":{
                    "fileHandler":{
                        "class":"logging.FileHandler",
                        "formatter":"myFormatter",
                        "filename":"config2.log"
                        }
                    },        
        "loggers":{
            "exampleApp":{
                "handlers":["fileHandler"],
                "level":"INFO",
                }
            },
 
        "formatters":{
            "myFormatter":{
                "format":"%(asctime)s - %(name)s - %(levelname)s - %(message)s"
                }
            }
        }
 
    logging.config.dictConfig(dictLogConfig)
 
    logger = logging.getLogger("exampleApp")
 
    logger.info("Program started")
    result = otherMod2.add(7, 8)
    logger.info("Done!")
 
if __name__ == "__main__":
    main()
```
运行该段代码，程序的输出结果与上面的一样。

## 总结
粗略的写完了这篇文章，大概知道了在程序中如何使用logging和配置logging的几种方式。会在以后的程序中尽量多使用log，避免使用print语句来调试语句，养成良好得习惯。

## 参考
* [An Intro to logging](http://www.blog.pythonlibrary.org/2012/08/02/python-101-an-intro-to-logging/)
* [Good logging practice in Python](http://victorlin.me/posts/2012/08/26/good-logging-practice-in-python)

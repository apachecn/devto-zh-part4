# Python 日志记录配置

> 原文：<https://dev.to/serhatteker/python-logging-configuration-59pp>

不，请不要用`print`调试你的程序。

Python 在标准库中附带了一个`logging`模块，为 Python 程序发出日志消息提供了一个灵活的框架。

该模块为应用程序提供了一些配置不同日志处理程序的方法，以及一种将日志消息路由到这些处理程序的方法。这允许高度灵活的配置，可以处理许多不同的用例。

日志记录功能是根据它们用来跟踪的事件的级别或严重性来命名的。标准等级及其适用性描述如下:

| 水平 | 级别代码 | 使用时 |
| --- | --- | --- |
| 调试 | Ten | 详细信息，通常仅在诊断问题时感兴趣。 |
| 信息 | Twenty | 确认事情按预期运行。 |
| 警告 | Thirty | 意外发生的迹象。该软件仍按预期工作。 |
| 错误 | Forty | 由于更严重的问题，软件无法执行某些功能。 |
| 批评的 | Fifty | 严重错误，表明程序本身可能无法继续运行。 |

更多详情: [Python 基础如何记录日志](https://docs.python.org/3/howto/logging.html)

## 基本日志记录

日志记录的一个非常简单的例子是:

```
import logging

logging.info("Hello, World!")
logging.warning('Watch out!') 
```

Enter fullscreen mode Exit fullscreen mode

如果您运行这个脚本，您将会看到:

```
WARNING:root:Watch out! 
```

Enter fullscreen mode Exit fullscreen mode

因为默认等级是`WARNING`，所以`INFO`消息不会出现。

## 从模块中记录

然而，更好的方法是将`__name__`与`DEBUG`日志记录级别一起使用。由于`__name__`包含了当前模块的全名，所以它可以在任何模块中工作。

```
import logging

logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)

logger.info("Hello, World!")
logger.warning('Watch out!') 
```

Enter fullscreen mode Exit fullscreen mode

输出将是:

```
INFO:__main__:Hello, World!
WARNING:__main__:Watch out! 
```

Enter fullscreen mode Exit fullscreen mode

## 记录到一个文件中

一种非常常见的情况是在文件中记录日志事件。

```
import logging

logging.basicConfig(filename='example.log', level=logging.DEBUG)
logger = logging.getLogger(__name__)

logger.debug('This message should go to the log file')
logger.info('So should this')
logger.warning('And this, too') 
```

Enter fullscreen mode Exit fullscreen mode

如果我们打开文件“example.log”，我们应该会发现日志消息:

```
DEBUG:__main__:This message should go to the log file
INFO:__main__:So should this
WARNING:__main__:And this, too 
```

Enter fullscreen mode Exit fullscreen mode

## 通过 INI 文件配置

对于大多数情况，这是最好的方法；

在项目根目录下创建一个文件名`logging.ini`，如下:

```
[loggers]
keys=root

[logger_root]
level=DEBUG
handlers=screen,file

[formatters]
keys=simple,verbose

[formatter_simple]
format=%(asctime)s [%(levelname)s] %(name)s: %(message)s

[formatter_verbose]
format=[%(asctime)s] %(levelname)s [%(filename)s %(name)s %(funcName)s (%(lineno)d)]: %(message)s

[handlers]
keys=file,screen

[handler_file]
class=handlers.TimedRotatingFileHandler
interval=midnight
backupCount=5
formatter=verbose
level=DEBUG
args=('debug.log',)

[handler_screen]
class=StreamHandler
formatter=simple
level=DEBUG
args=(sys.stdout,) 
```

Enter fullscreen mode Exit fullscreen mode

或者`curl`它:

```
$ curl -o logging.ini https://gist.githubusercontent.com/SerhatTeker/13d31a2159cd88c31a8cd464df115789/raw/81d9bbe850799e71b6095a6a43fa65dc1cf0e33c/logging.ini 
```

Enter fullscreen mode Exit fullscreen mode

然后配置如下；

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*- 
import logging

from logging.config import fileConfig

fileConfig('logging.ini')
logger = logging.getLogger(__name__)

name = "YourName"

logger.info(f"Hello {name}")
logger.debug('This message should go to the log file')
logger.info('So should this')
logger.warning('And this, too') 
```

Enter fullscreen mode Exit fullscreen mode

如果您运行这个脚本，`sysout`将会是:

```
2019-07-14 18:15:13,161 [INFO] __main__: Hello YourName
2019-07-14 18:15:13,161 [DEBUG] __main__: This message should go to the log file
2019-07-14 18:15:13,161 [INFO] __main__: So should this
2019-07-14 18:15:13,161 [WARNING] __main__: And this, too 
```

Enter fullscreen mode Exit fullscreen mode

并且`debug.log`文件应该包含:

```
[2019-07-14 18:12:58,910] INFO [logging_ini.py __main__ <module> (14)]: Hello YourName
[2019-07-14 18:12:58,910] DEBUG [logging_ini.py __main__ <module> (15)]: This message should go to the log file
[2019-07-14 18:12:58,910] INFO [logging_ini.py __main__ <module> (16)]: So should this
[2019-07-14 18:12:58,910] WARNING [logging_ini.py __main__ <module> (17)]: And this, too 
```

Enter fullscreen mode Exit fullscreen mode

完成了。
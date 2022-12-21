# 使用 Python 进行日志记录

> 原文：<https://dev.to/petercour/logging-with-python-4i7p>

[![](img/dfd0181651eb603c86dae7cfa25b6963.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ON170FKN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmetvcdn.metv.com%252F1JMWa-1461171702-3463-list_items-computers_voyage.gif%26f%3D1)

闪烁的灯并不能真正帮助解决问题。每个灯代表什么？在错误的情况下，你不能通过这种方式找到错误。

如果出现问题，日志记录有助于调试程序。更多的数据意味着你可以更好地分析。

### 用 Python 记录日志

Python 附带了`logging`模块。您可以编写这样的日志消息:

```
logger.info("Hello World printed") 
```

下面的简单程序记录到一个文件中。然后，日志文件可用于分析。

```
#!/usr/bin/python3
import logging

logging.basicConfig(level=logging.INFO, 
                    filename='my_app.log', # log to this file
                    format='%(asctime)s [INFO] %(message)s') # include timestamp

logger = logging.getLogger()
logger.setLevel(logging.INFO)

def main():
    logger.info("Program started")
    print("Hello World")
    logger.info("Hello World printed")
    logger.info("Program finished")

main() 
```

[![](img/3d6904c8ae355b769b87dba2a82d36b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ifQwMhHQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252Ft4aBVelzr49qw%252F200.gif%26f%3D1)

### 格式化

默认情况下，记录器模块不包括时间。时间有助于找到你的错误，因为你可以看到事情发生的时间。

该日志文件包含如下内容:

```
2019-07-05 14:44:02,052 [INFO] Program started
2019-07-05 14:44:02,053 [INFO] Hello World printed
2019-07-05 14:44:02,053 [INFO] Program finished 
```

第一行格式化日志消息，以防您想要更改:

```
#!/usr/bin/python3
logging.basicConfig(level=logging.INFO, 
                    filename='my_app.log', # log to this file
                    format='%(asctime)s [INFO] %(message)s') 
```

学习 Python:

*   [https://pythonbasics.org](https://pythonbasics.org)
*   [https://pythonprogramminglanguage.com](https://pythonprogramminglanguage.com)
*   [https://python.org](https://python.org)
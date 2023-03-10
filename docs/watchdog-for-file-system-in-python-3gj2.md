# Python 中文件系统的看门狗

> 原文：<https://dev.to/petercour/watchdog-for-file-system-in-python-3gj2>

看门狗模块可以监控文件系统的变化。这是一个 Python 模块。文件系统的变化无时无刻不在发生，保存文件，删除文件等等。

假设您监控下载目录。快点，这是怎么回事？

[![](img/329b9b95408b6798635e19739d34eed7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rw6rawZP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252F3oz8xwjA7yu1VGIaac%252Fgiphy.gif%26f%3D1)

运行监视器脚本。假设您监控文件系统上的下载目录。

```
python3 watch.py /home/YOURNAME/Downloads 
```

进入下载目录，并在不同的终端标签中创建新文件。

```
cd Downloads
echo "test" > tmp.txt 
```

该脚本会告诉您文件系统已经更改

```
2019-07-17 13:37:32 - Created file: /home/YOURNAME/Downloads/tmp.txt
2019-07-17 13:37:32 - Modified directory: /home/YOURNAME/Downloads
2019-07-17 13:37:32 - Modified file: /home/YOURNAME/Downloads/tmp.txt 
```

代码非常简单:

```
#!/usr/bin/python3  
#-*- coding: utf-8 -*-                                                                                                                                              

import sys
import time
import logging
from watchdog.observers import Observer
from watchdog.events import LoggingEventHandler

if __name__ == "__main__":
    logging.basicConfig(level=logging.INFO,
                        format='%(asctime)s - %(message)s',
                        datefmt='%Y-%m-%d %H:%M:%S')
    path = sys.argv[1] if len(sys.argv) > 1 else '.'
    event_handler = LoggingEventHandler()
    observer = Observer()
    observer.schedule(event_handler, path, recursive=True)
    observer.start()
    try:
        while True:
            time.sleep(1)
    except KeyboardInterrupt:
        observer.stop()
    observer.join() 
```

首先导入看门狗模块。创建一个 Observer 对象，将其设置为 schedule 和 recursive。那就开始吧。

相关链接:

*   [看门狗模块](https://github.com/gorakhargosh/watchdog)
*   [学习 Python](https://pythonbasics.org/)
*   [Python 练习](https://pythonbasics.org/exercises/)
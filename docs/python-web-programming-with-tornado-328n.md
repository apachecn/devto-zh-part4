# 用 Tornado 进行 Python Web 编程

> 原文：<https://dev.to/petercour/python-web-programming-with-tornado-328n>

创建 web 应用程序有许多模块。Django 和 Flask 是最受欢迎的。但是还有其他的，比如龙卷风。

龙卷风是如何工作的？其实挺简单的。单个单页应用程序是:

```
#!/usr/bin/python3
# -*- coding: utf-8 -*-

import tornado.ioloop   
import tornado.web

class HomeHandler(tornado.web.RequestHandler): 
    def get(self):
        self.write("hello, world")

app = tornado.web.Application([ (r"/", HomeHandler), ])
app.listen(8000)
tornado.ioloop.IOLoop.instance().start() 
```

在 web 浏览器上打开本地主机端口 8000，查看 hello world 消息。脚本本身在终端中不输出任何东西。

初始化龙卷风时可以创建不同的路线。Web.Application()调用。每条路线都是一个类。

```
#!/usr/bin/python3
# -*- coding: utf-8 -*-

import tornado.ioloop 
import tornado.web

class HomeHandler(tornado.web.RequestHandler): 
    def get(self):
        self.write("hello, world")

class AboutHandler(tornado.web.RequestHandler): 
    def get(self):
        self.write("Mr President")

app = tornado.web.Application([
    (r"/", HomeHandler), 
    (r"/about", AboutHandler)
])
app.listen(8000)
tornado.ioloop.IOLoop.instance().start() 
```

然后就可以打开/路线或者/关于路线了。

相关链接:

*   [龙卷风网络服务器](https://www.tornadoweb.org/en/stable/)
*   [学习 Python](https://pythonbasics.org/)
*   [Python 练习](https://pythonbasics.org/exercises/)
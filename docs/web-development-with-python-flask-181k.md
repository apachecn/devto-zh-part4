# 使用 Python Flask 进行 Web 开发

> 原文：<https://dev.to/petercour/web-development-with-python-flask-181k>

Flask 允许您用 [Python](https://pythonprogramminglanguage.com/) 进行网络开发。Python 可以运行返回 HTTP 响应的网络服务器。

那么 Flask 是什么？Flask 是一个微型框架。帮助您构建 web 应用程序的 Python 模块。

### 代码

首先用 pip 安装烧瓶，

```
pip install flask 
```

我们将在 Python 脚本中导入该模块:

```
#!/usr/bin/python3                                                                                                                                                  
# -*- coding: utf-8 -*                                                                                                                                              
from flask import Flask
from flask import request
from flask import make_response 
```

然后运行下面的程序

```
#!/usr/bin/python3                                                                                                                                                  
# -*- coding: utf-8 -*                                                                                                                                              
from flask import Flask
from flask import request
from flask import make_response

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

@app.route('/browser')
def index():
    user_agent = request.headers.get('User-Agent')
    return '<p>Your browser is %s</p>' % user_agent

if __name__ == '__main__':
    app.run() 
```

在您的浏览器中，您可以打开 [http://127.0.0.1:5000/](http://127.0.0.1:5000/) 和[http://127 . 0 . 0 . 1:5000/browser](http://127.0.0.1:5000/browser)。

[![](img/112180af34473fb0d29a3c34a51f268b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6K4Vnzl---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252Fl1J9COxiSAD6xyx20%252Fgiphy.gif%26f%3D1)

方法@app.route()将 web 浏览器 url 链接到 Python 函数。Python 函数可以做任何你想做的事情。

响应可以返回纯文本和 html 响应。调用 app.run()启动服务器。

相关链接:

*   [Flask Web Apps 课程&例题](https://gumroad.com/l/IMzBy)
*   [砂箱模块](http://flask.pocoo.org/)
*   [烧瓶条](https://pythonspot.com/flask-web-app-with-python/)
*   [学习 Python 编程](https://pythonprogramminglanguage.com/)
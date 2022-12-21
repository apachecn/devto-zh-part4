# 使用 pipenv 创建新的 Flask 项目

> 原文：<https://dev.to/therealdarkmage/creating-a-new-flask-project-with-pipenv-i93>

*   创建 pipenv 环境

```
$ mkdir project
$ cd project
$ pipenv install 
```

Enter fullscreen mode Exit fullscreen mode

* * *

*   安装闪存

```
$ pipenv install flask 
```

Enter fullscreen mode Exit fullscreen mode

* * *

*   结构文件夹/文件:

```
mkdir app
touch app/__init__.py
touch app/routes.py
touch microblog.py 
```

Enter fullscreen mode Exit fullscreen mode

* * *

*   在`app/__init__.py`中:

```
from flask import Flask

app = Flask(__name__)

from app import routes 
```

Enter fullscreen mode Exit fullscreen mode

* * *

*   在`app/routes.py`中:

```
from app import app

@app.route('/')
@app.route('/index')
def index():
    return "Hello, World!" 
```

Enter fullscreen mode Exit fullscreen mode

* * *

*   在`microblog.py`中:

```
from app import app 
```

Enter fullscreen mode Exit fullscreen mode

* * *

*   换成管道:

```
$ pipenv shell 
```

Enter fullscreen mode Exit fullscreen mode

* * *

*   设置`FLASK_APP`变量:

```
$ export FLASK_APP=microblog.py 
```

Enter fullscreen mode Exit fullscreen mode

* * *

*   运行应用程序:

```
$ flask run 
```

Enter fullscreen mode Exit fullscreen mode

* * *

现在，无论何时你需要扩展你的 Flask 应用程序来导入一些新的库或框架或任何东西，只需使用 pipenv 安装它。

* * *

这整个过程启发我编写了一个 bash 脚本，为您自动完成所有这些工作。为什么要让事情变得不必要的困难呢？

抓取 [autoflask.sh](https://github.com/mikedesu/autoflask.sh) 并在您的系统上旋转一下，快速获得 flask 中的一个准系统 Hello World 项目，今天开始！

* * *

# [如果你需要一名计算机科学导师、代码审核员，或者仅仅是一个可以和程序配对的人，请联系我](https://codementor.io/mikebell66)
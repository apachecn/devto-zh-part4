# 构造一个反应单报告的烧瓶

> 原文：<https://dev.to/wrrnwng/structure-a-flask-react-monorepo-1eb9>

这是我在同一个 git 存储库中的 Flask 服务器和 React 客户端的项目结构。这与许多 NodeJS、React [monorepos](https://en.wikipedia.org/wiki/Monorepo) 非常相似，其中服务器代码将位于`server`子目录中，而客户端代码将位于`client`子目录中。

首先，让我们开始创建项目目录并初始化 git 存储库:

```
mkdir flask-react-monorepo
cd flask-react-monorepo
git init 
```

Enter fullscreen mode Exit fullscreen mode

让我们用 [`venv`](https://docs.python.org/3/library/venv.html) 为回购创建一个虚拟环境，并将其放在项目内的`venv`目录中。我们也想把这个添加到我们的`.gitignore`文件中。一旦我们建立了虚拟环境，我们需要让我们的系统知道如何使用它。

```
python -m venv venv
echo venv/ > .gitignore
source venv/bin/activate 
```

Enter fullscreen mode Exit fullscreen mode

让我们安装我们的依赖项，首先是 Python，然后在一个名为`requirements.txt`的文件中保存一个依赖项列表。

```
pip install Flask
pip freeze > requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

`requirements.txt`(您的版本可能有所不同)

```
Click==7.0
Flask==1.0.3
itsdangerous==1.1.0
Jinja2==2.10.1
MarkupSafe==1.1.1
Werkzeug==0.15.4 
```

Enter fullscreen mode Exit fullscreen mode

构造 flask 应用程序的规范方法是用一个 Python 包命名，实际应用程序应该在子目录中。查看烧瓶教程[这里](http://flask.pocoo.org/docs/1.0/tutorial/layout/)了解更多细节。

现在，让我们称 Flask 服务器为`server`。它很容易成为`api`或者你喜欢的其他东西。

```
mkdir server 
```

Enter fullscreen mode Exit fullscreen mode

为了让我们的 Flask 设置工作，我们需要在项目目录的根目录下创建一个名为`setup.py`的文件:

`setup.py`

```
from setuptools import setup

setup(
    name='server',
    packages=['server'],
    include_package_data=True,
    install_requires=['flask']
) 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们通过在我们的`server`目录中有一个`__init__.py`文件，在`server` Python 包中建立一个简单的 Flask 应用程序。

`server/ __init__.py`

```
from flask import Flask

app = Flask( __name__ )

@app.route('/')
def index():
    return 'Hello, World!' 
```

Enter fullscreen mode Exit fullscreen mode

现在，集成 JavaScript 客户端的有趣部分来了。我将简单地使用`create-react-app`来实现这一点，但是您可以用任何您喜欢的前端框架或 CLI 工具来替换它。

从项目根:

```
create-react-app client 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以从项目根目录键入`FLASK_APP=server flask run`来运行我们的开发服务器，并且从另一个终端，从`client`目录运行`yarn start`来运行开发客户端，但是这对我来说太多的步骤了。

为了简化开发过程，我还将在项目根中使用`yarn`，并安装 [`concurrently`](https://www.npmjs.com/package/concurrently) 包。

从根目录:

```
yarn init
yarn add -D concurrently
echo node_modules/ >> .gitignore 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们给生成的`package.json`文件`yarn init`添加一些脚本。我希望`yarn client`运行客户端开发服务器，而`yarn server`运行后端开发服务器。我也想从根子上叫`yarn start`来兼管这两者。

```
{  "name":  "flask-react-monorepo",  "version":  "1.0.0",  "main":  "index.js",  "author":  "Warren Wong <me@warrenwong.org>",  "license":  "MIT",  "devDependencies":  {  "concurrently":  "^4.1.0"  },  "scripts":  {  "start":  "concurrently \"yarn client\"  \"yarn server\"",  "client":  "cd client && yarn start",  "server":  "FLASK_APP=server flask run"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，默认情况下，您的后端将在端口 [5000](http://localhost:5000) 上，而您的客户端将在端口 [3000](http://localhost:3000) 上。
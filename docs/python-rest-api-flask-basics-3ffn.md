# 烧瓶支架 API:烧瓶基础

> 原文：<https://dev.to/dbanty/python-rest-api-flask-basics-3ffn>

## 本帖如何运作

1.  这篇文章是剖析我使用 Python 和 Flask 创建 REST API 的方法的系列文章的一部分。
2.  每篇文章都是在假设读者已经阅读并理解了本系列之前所有文章的基础上撰写的。
3.  我会尝试解释一路走来所做的每一个选择。如果你觉得我遗漏了什么，请在下面的评论中提出来。
4.  每一节都解释了为什么以及如何使用特定的技巧或技术。随意跳过你已经熟悉的部分。
5.  为这个系列生成的源代码可以在 GitHub 上找到。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[dban ty](https://github.com/dbanty)/[python-rest](https://github.com/dbanty/python-rest)

### 展示基本 Flask REST API 的示例项目

<article class="markdown-body entry-content container-lg" itemprop="text">

你好！我创建了这个回购来跟踪关于 Flask 的一系列博客文章，但是我从来没有完成它，也永远不会完成。实际上，[已经停止使用 Flask，转而使用 FastAPI](https://dev.to/dbanty/why-i-m-leaving-flask-3ki6) ，并建议您也这样做，以获得创建 Python REST APIs 的真正美妙体验。

</article>

[View on GitHub](https://github.com/dbanty/python-rest)

## 在本帖中

这篇文章使用工厂模式创建了一个非常简单的 Flask 应用程序。然后，它通过使用 pytest 的测试驱动开发，在蓝图中创建一个简单的健康检查控制器。在这篇文章的最后，你已经有了一个非常简单的 web 服务器，并且有完整的测试覆盖范围！

### 主题:

1.  烧瓶工厂模式
2.  文档字符串
3.  键入注释
4.  测试驱动开发
5.  pytest
6.  烧瓶设计图

## 烧瓶工厂模式

Flask 的整体以`Flask` app 对象为中心，开始使用 Flask 的第一件事就是在项目中的某个地方创建一个这样的对象。有两种常见的方法可以做到这一点，您将在教程中看到:

1.  在一个文件(通常称为“app.py”)中创建对象，该文件稍后将由为其提供服务的任何服务器导入。这种方法的主要好处是样板文件较少，您可以快速启动并运行，这就是为什么它是教程的流行选择。

2.  声明一个“工厂方法”,它返回一个应用程序的实例。这种方法需要更多的输入，但是有很多好处:

    *   通过将导入放在工厂方法中来延迟导入，以避免循环依赖(当您开始使用扩展时，这非常重要)
    *   延迟扩展的初始化(使单元测试更容易打补丁)
    *   允许您针对不同的使用情形动态配置应用程序(例如，测试与开发和生产)
    *   更多信息，请点击查看文档

我总是推荐使用工厂方法，它很容易上手。首先，在模块的基类`__init__.py`中创建方法本身。

```
# python_rest/__init__.py from flask import Flask

def create_app():
    return Flask(__name__) 
```

Enter fullscreen mode Exit fullscreen mode

这就是声明应用程序的所有代码！现在我们只需要用 Flask 开发服务器运行它。

### 运行在命令行上

我们可以很容易地在命令行上运行带有诗歌的应用程序，但首先我们必须告诉 Flask 要运行什么。幸运的是，它内置了对环境文件的支持，让我们可以声明各种各样的东西。首先，添加`python-dotenv`作为开发依赖。

```
$ poetry add -D python-dotenv 
```

Enter fullscreen mode Exit fullscreen mode

现在在项目的根目录下创建一个名为。它将环境变量“FLASK_APP”设置为您的模块的名称。

> 注意:烧瓶建议检查。flaskenv "文件插入到项目中。然后，您可以用更标准的来覆盖它们。env”文件，该文件不应签入源代码管理。

```
# .flaskenv
FLASK_APP=python_rest 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经准备好运行开发服务器了。确保你把它放在诗歌下面！

```
$ poetry run flask run 
```

Enter fullscreen mode Exit fullscreen mode

### 奔跑在皮查姆

1.  在屏幕顶部，单击“添加配置”
2.  单击“+”
3.  选择“烧瓶服务器”
4.  将“目标类型”设置为“模块名称”
5.  将“目标”设置为您的模块名称(例如“python_rest”)
6.  单击“确定”
7.  选择新配置后，单击绿色箭头开始运行

🎉万岁！现在，您已经有了一个运行在本地机器端口 5000(默认)上的 web 服务器！它还没有做任何事情，但我保证它正在运行。

### 文档字符串

既然您已经编写了一个 Python 函数，那么您肯定应该为该函数编写一个 docstring。记录代码的理由是无穷无尽的，我发现最好的做法是给每个函数一个 docstring，至少有两个单词的摘要。文档字符串对于以后为 API 创建良好的文档很重要，所以最好马上养成这个习惯。

文档字符串有很多格式，大多数都受 PyCharm 支持，并且可以在 py charm 中呈现。本页给出了不同格式的一个很好的概述。我使用 ReStructuredText 字符串只是因为我在其他类型的项目中使用的 Sphinx 本身支持它们。他们看起来像这样:

```
def create_app():
    """
    Creates an application instance to run
    :return: A Flask object
    """
    return Flask(__name__) 
```

Enter fullscreen mode Exit fullscreen mode

### 类型标注

我不能强调我认为类型注释有多重要。Python 的动态类型系统非常棒，使得编写代码非常快。然而，随着项目变得越来越复杂，它很容易让你犯一些简单的错误。我输入所有的提示，这样 PyCharm 就可以告诉我什么时候我做了什么蠢事(比如忘记了某个地方的返回)，这样静态分析工具(比如 mypy)就可以捕获一些常见的 bug。我们的`create_app`函数的类型注释如下:

```
def create_app() -> Flask: 
```

Enter fullscreen mode Exit fullscreen mode

### 测试驱动开发

测试驱动开发，或者 TDD，是在你写代码之前为你的代码写测试的简单行为。有整本书都写了为什么这是个好主意，但很简单，这就是我为什么这么做:

*   你总是知道你什么时候结束
*   您可以更加自信地进行重构
*   在开始之前，帮助你思考边缘情况和不愉快的道路
*   鼓励模块化和解耦设计

### Pytest

为了编写测试，你需要使用某种框架。Python 标准库中有一个叫做“Unittest”的，但是我更喜欢使用 [pytest](https://docs.pytest.org/en/latest/) 。使用它的原因有很多，但最基本的是:

*   很少样板文件
*   大量社区插件

我在测试 Flask 应用时经常使用的一个插件是 [pytest-flask](https://pytest-flask.readthedocs.io/en/latest/tutorial.html) ，它提供了许多有用的工具。要开始测试，安装 pytest 和 pytest-flask 作为开发依赖项。

```
$ poetry add -D pytest pytest-flask 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您正在使用 PyCharm，您将需要配置它来使用 pytest

1.  打开偏好设置
2.  单击工具
3.  转到 Python 集成工具
4.  将“默认测试运行程序”更改为“pytest”

> 注意:您也可以在这里更改 Docstring 格式

接下来，在 tests 目录中为 pytest 创建配置文件(名为 conftest.py)。这个文件总是在测试运行之前被导入，在这里您将想要定义您想要使用的任何装置，并执行任何其他的预先测试设置。要使用 pytest-flask，我们必须定义一个“app”fixture，它返回我们的`Flask`对象的一个实例。

```
# tests/conftest.py 
import pytest
from flask import Flask

@pytest.fixture
def app() -> Flask:
    """ Provides an instance of our Flask app """
    from python_rest import create_app

    return create_app() 
```

Enter fullscreen mode Exit fullscreen mode

### 蓝图

现在是时候创建一个我们可以触及的端点了。一种非常常见的做法是使用某种健康检查端点来验证您的应用程序正在运行。为了创建这个端点，我们需要创建一个控制器(处理请求的函数),它将注册到一个蓝图(路径下的控制器集合),这个蓝图又将注册到我们的 app 对象。

首先，让我们弄清楚把这个新代码放在哪里。我喜欢将我创建的任何蓝图保存在主模块下的一个名为“蓝图”的模块中。这个蓝图将用于管理根路径(“/”)，所以我将它放在“蓝图”模块下一个名为“root.py”的文件中。

接下来，我们应该通过创建“tests/test _ blue prints/test _ root . py”在 tests 目录中镜像这个目录结构。连同适当的`__init__.py`文件，整个项目结构现在应该如下所示:

```
python-rest
|-- README.md
|-- poetry.lock
|-- pyproject.toml
|-- python_rest
|   |-- __init__.py
|   `-- blueprints
|       |-- __init__.py
|       `-- root.py
`-- tests
    |-- conftest.py
    `-- test_blueprints
        |-- __init__.py
        `-- test_root.py 
```

Enter fullscreen mode Exit fullscreen mode

让我们为即将创建的端点编写测试。我们可以使用 pytest-flask 提供的客户机夹具来适当地执行请求。我们希望健康检查返回一个状态 200 (OK ),并带有一些 JSON 主体。下面是测试:

```
# tests/test_blueprints/test_root.py 
from http import HTTPStatus

def test_health(client):
    response = client.get('/health/')
    assert response.status_code == HTTPStatus.OK, 'Health check failed'
    assert response.json == {'message': 'Healthy'}, 'Improper response' 
```

Enter fullscreen mode Exit fullscreen mode

注意，那些断言语句是在运行时导致测试通过或失败的东西。断言后逗号后面的字符串是检查失败时将显示的消息。通过单击 PyCharm 中函数定义旁边的绿色小箭头，或者通过在命令行上运行`poetry run pytest`来运行测试。不管怎样，你应该看到它失败了。

现在让我们创建根蓝图和实际的端点。

```
# python_rest/blueprints/root.py 
from flask import Blueprint

# Declare the blueprint with whatever name you want to give it root_blueprint = Blueprint('root', __name__)

 # This is how you register a controller, it accepts OPTIONS and GET methods by default @root_blueprint.route('/health/')
def health():
    return {'message': 'Healthy'}  # This will return as JSON by default with a 200 status code 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要一种方法来将这个蓝图注册到应用程序中，这样我们就可以实际访问控制器。实现这一点的方法是在应用程序创建时使用一个名为的函数，它将蓝图注册到应用程序中。惯例是将这个函数命名为“init_app ”,我喜欢有一个“init_app”函数，它可以一次注册所有的蓝图。合理的做法是将这个函数放在蓝图模块的`__init__.py`中。

```
# python_rest/blueprints/__init__.py 
from flask import Flask

def init_app(app: Flask):
    from .root import root_blueprint
    app.register_blueprint(root_blueprint) 
```

Enter fullscreen mode Exit fullscreen mode

现在你只需要从你的 create_app 工厂函数中调用这个函数。

```
# python_rest/__init__.py 
from flask import Flask

def create_app() -> Flask:
    """
    Creates an application instance to run
    :return: A Flask object
    """
    app = Flask(__name__)

    from . import blueprints
    blueprints.init_app(app)

    return app 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！如果您再次运行测试，它现在应该通过了！您也可以运行 flask 应用程序，然后在浏览器中导航到`localhost:5000/health/`来查看您的 JSON 响应。
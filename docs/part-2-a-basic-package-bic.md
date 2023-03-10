# 第 2 部分基本套装

> 原文：<https://dev.to/thefern/part-2-a-basic-package-bic>

在上一篇文章中，我们讨论了什么是包，这是一个很好的介绍。我宁愿向你展示如何建立一个，我们不会疯狂，只是足够向你展示基本的。

## 准系统结构

为了创建一个包，我们需要一个文件夹结构。py 项目 a 包技术上不需要 [py-modules doc](https://packaging.python.org/guides/distributing-packages-using-setuptools/#py-modules) 。然而，大多数人可能会在他们的项目中有几个包来组织事情。(注意:在后面的文章中我会谈到 cookiecutter 和模板)

```
├── README.md
├── awesome_package
│   ├── __init__.py
│   └── hello_world.py
└── setup.py 
```

Enter fullscreen mode Exit fullscreen mode

下面是上述包结构的链接

*   [准系统-01](https://github.com/kodaman2/A_Basic_Package/tree/Barebones-01)

## 设置

/setup.py 文件是包中最重要的文件。它包含您的软件包需要的所有信息。我们将在后面的文章中更详细地讨论这一点。现在，下面的内容足以让我们开始运行。

```
from setuptools import setup, find_packages

setup(
    name='my_tool',
    version='0.0.1',
    packages=find_packages(exclude=['contrib', 'docs', 'tests*']),
) 
```

Enter fullscreen mode Exit fullscreen mode

*   [Setup.py-02](https://github.com/kodaman2/A_Basic_Package/tree/Setup.py-02)

## 让我们添加一些 python 代码

我们不会发疯，我们会在 hello_world 文件中添加一个函数

```
def say_hello(name):
    print("hello %s" % (name)) 
```

Enter fullscreen mode Exit fullscreen mode

## 将我们的函数添加到 init

```
from .hello_world import * 
```

Enter fullscreen mode Exit fullscreen mode

我们正在导入 hello_world 中的所有函数，在我们的例子中只有一个函数。好了，我们准备好测试我们的包了。

## 作为开发包安装

一些需要注意的事情。用 development 参数安装一个包允许我们进行修改，而不必通过一个`pip --upgrade`命令不断安装或更新。如果你正在做一件需要几天、几周或者更长时间的事情，这会让你感到烦心。

另一件值得一提的事情是，如果您进行了更改，并且已经在终端中启动了 python，那么您必须退出()，然后重新启动 python。别问我怎么知道这个的。

在包内，通过运行下面的命令来安装它。

```
python3 setup.py develop 
```

Enter fullscreen mode Exit fullscreen mode

检查以确保软件包已安装。

```
pip list
...
my-tool 0.0.1 /Users/terminalX/git/A_Basic_Package
... 
```

Enter fullscreen mode Exit fullscreen mode

## 测试我们的包

在终端中启动 python，并测试我们的包，如下所示。太简单了，伙计。正如澳大利亚人所说。

```
>>> from awesome_package import *
>>> say_hello("fer")
hello fer 
```

Enter fullscreen mode Exit fullscreen mode

*   [测试-03](https://github.com/kodaman2/A_Basic_Package/tree/Test-03)

# 从 Github 安装包

现在我们有了一个完整的工作包，我们可以卸载开发安装，直接从 Github 安装

```
pip3 uninstall my_tool
pip3 install git+https://github.com/kodaman2/A_Basic_Package.git 
```

Enter fullscreen mode Exit fullscreen mode

确保您仍然可以使用`say_hello()`功能。

**提示:如果你想知道一个模块中有哪些方法:**

```
>>> dir(hello_world)
[' __builtins__', ' __cached__', ' __doc__', ' __file__', ' __loader__', ' __name__', ' __package__', ' __spec__', 'say_hello'] 
```

Enter fullscreen mode Exit fullscreen mode

## 下一篇

请确保您使用这个包，添加更多的功能，更多的包，看看会发生什么。请记住，如果您决定继续使用这个包，请卸载它，并按照开发部分中的说明进行安装。

在下一篇文章中，我们将创建一个更高级的示例，同时学习如何在其他 python 工具的帮助下高效地创建包。感谢阅读。
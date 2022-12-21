# 设置您的开发环境

> 原文：<https://dev.to/wangonya/setting-up-your-development-environment-38m1>

在本帖中，我们将介绍使用 Python 的基础知识，并点击创建 CLI 应用程序。我们将安装 Python 3，设置我们的开发环境，安装 Click，并创建一个`hello-world`应用程序。

## Python 3.x

开始使用 Python 很容易。大多数 <sup>*</sup> nix 系统都预装了 Python。我们将使用 Python 3。要检查您使用的 Python 版本，请打开您的终端并运行`python -V`。您应该得到类似如下的输出:

```
Python 2.7.10 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我电脑上安装的默认 Python 版本是`2.7.10`。默认情况下，大多数 MAC 都附带 Python 2。如果你有一个`3.x.x`，那么你就可以走了。安装 Python 3 后，运行`python3 -V`输出:

```
Python 3.7.3 
```

Enter fullscreen mode Exit fullscreen mode

如果你没有安装 Python，你可以去[python.org](https://www.python.org/downloads/)，为你的系统下载合适的安装程序，然后运行它。如果你使用的是 Windows，这一步是必要的。

## 皮普

`pip`是 Python 的一个包管理器。它允许我们安装项目中需要使用的库和依赖项。它预装了 Python。要检查是否有，可以运行`pip -V`或`pip3 -V`。您应该会看到如下内容:

```
pip 19.0.3 from /usr/local/lib/python3.7/site-packages/pip (python 3.7) 
```

Enter fullscreen mode Exit fullscreen mode

## Virtualenv

在处理 Python 项目时，创建独立的工作环境总是一个好主意。这是因为不同的项目将需要不同的依赖项或这些依赖项的不同版本。在全球范围内安装这些软件将会很难管理，而且只会搞乱你的系统。将它们安装在项目环境中可以保持整洁。在 Python 中有几个处理环境的选项，但是我们将把重点放在`virtualenv`上。

我们将创建一个新目录，并在其中创建一个虚拟环境。在您的终端上运行:

```
$ mkdir hello-world-cli && cd hello-world-cli 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令创建了一个名为`hello-world-cli`的新目录，并进入该目录。

接下来，我们将使用`pip`安装`virtualenv`并创建我们的虚拟环境。

```
$ pip3 install virtualenv 
```

Enter fullscreen mode Exit fullscreen mode

```
$ virtualenv venv 
```

Enter fullscreen mode Exit fullscreen mode

我们的目录中现在有了一个 Python 虚拟环境。要使用它，我们需要通过运行:
来激活它

```
$ source venv/bin/activate
(venv) $ 
```

Enter fullscreen mode Exit fullscreen mode

当环境被激活时，您会注意到终端提示符的变化。环境名称在提示符前面。这就是你如何知道你在环境中工作。

## 点击

现在我们已经准备好了环境，我们可以开始我们的项目了。Python 提供了[多个包](https://docs.python-guide.org/scenarios/cli/)来帮助创建 CLI 应用。我们将使用[点击](https://click.palletsprojects.com/en/7.x/)，所以我们需要安装它来开始。

```
(venv) $ pip install Click 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们开始的全部内容。

### 你好世界！

像往常一样，我们将以“你好，世界！”开始程序。

仍然在`hello-world-cli`目录下，创建一个名为`hello-world.py` :
的文件

```
(venv) $ touch hello-world.py 
```

Enter fullscreen mode Exit fullscreen mode

在您喜欢的文本编辑器中打开文件，并添加以下代码:

```
import click

@click.command()
def hello():
    click.echo('Hello World!')

if __name__ == '__main__':
    hello() 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看看每一行都发生了什么。

```
import click 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们导入了`click`,这样我们就可以用它来创建我们的命令。

```
@click.command() 
```

Enter fullscreen mode Exit fullscreen mode

每当你在 Python 中看到一个函数上面有一个`@`符号，那就是装饰器。不用深入装饰者的很多细节，理解(目前)他们简单地修改他们“装饰”的函数的行为就足够了。Click 使用 decorators 的概念将 Python 函数转换成可以通过终端直接执行的命令。这里的装饰器将`hello()`转换成一个命令。稍后我们将了解更多关于命令的内容。

```
def hello(): 
```

Enter fullscreen mode Exit fullscreen mode

这是我们在 Python 中定义函数的方式。

```
 click.echo('Hello World') 
```

Enter fullscreen mode Exit fullscreen mode

调用`hello()`将运行`click.echo('Hello World')`，显示文本“Hello World！”在终端上。

```
if __name__ == '__main__': 
```

Enter fullscreen mode Exit fullscreen mode

这是我们脚本的主要入口点。

尽管我自己经常使用它，但我需要一些帮助来了解它到底做了什么。网上的解释过于复杂，所以我向优秀的开发者社区寻求帮助。您可以关注本帖中的讨论:

[![wangonya](img/f24a24d0096b366df3522b3e07664d45.png)](/wangonya) [## Python 中的 if __name__ == '__main__ ':有什么用？

### kelvin wango nya Aug 3 ' 191min read

#discuss #explainlikeimfive #python](/wangonya/what-s-the-use-of-if-name-main-in-python-3eo5)

```
 hello() 
```

Enter fullscreen mode Exit fullscreen mode

这调用了我们的功能/命令。

好了，该看结果了。保存文件，回到终端，运行程序:

```
(venv) $ python hello-world.py
Hello World! 
```

Enter fullscreen mode Exit fullscreen mode

恭喜你走到这一步🎉

我们会在接下来的帖子里做更多酷的东西。

## 练习锻炼

我已经编辑了一些代码，所以我们现在传递单词“World！”为了显示“Hello World！”全部。**问题:代码不完整**。看你能不能顺利完成。

```
import click

@click.command()
def hello():
    click.echo('Hello {}')

if __name__ == '__main__':
    hello("World!") 
```

Enter fullscreen mode Exit fullscreen mode

<small>印度语</small>
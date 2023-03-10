# 向 CLI 命令添加选项

> 原文：<https://dev.to/wangonya/adding-options-to-cli-commands-1igh>

在之前的文章中，我们使用了`setuptools`来打包我们的应用程序，使我们能够使用一个可执行的命令`hello`来运行它。在本帖中，我们将探讨如何使用选项使 CLI 应用程序更具功能性和交互性。

## 选项

选项用于改变命令的默认行为。这通常是通过以`--option`或简称`-o`的形式传入参数来实现的。

让我们假设不只是打印出“你好，世界！”，我们希望可以选择全部用小写字母或全部用大写字母打印。我们可以通过添加一个`option()`装饰器来添加这个功能。

```
# helloworld.py 
import click

@click.command()
@click.option('-c', '--case', type=click.Choice(['upper', 'lower']))
def hello(case):
    if case:
        click.echo('You selected {} case.'.format(case))
    # click.echo('Hello World!') 
```

Enter fullscreen mode Exit fullscreen mode

~~每次对代码进行更改时，我们都必须重新安装应用程序并运行它来查看更改。~~
编辑:这个我错了。您唯一需要重新安装的时候是您对`setup.py`进行更改的时候。感谢[费尔南多](https://dev.to/kodaman2)的提醒。

然后运行应用程序，传入创建的选项:

```
(venv) $ hello -c upper  # --case would also work
You selected upper case. 
```

Enter fullscreen mode Exit fullscreen mode

```
(venv) $ hello -c lower
You selected lower case. 
```

Enter fullscreen mode Exit fullscreen mode

现在尝试传入一些其他选项，比如“normal”:

```
(venv) $ hello -c normal
Usage: hello [OPTIONS]
Try "hello --help" for help.

Error: Invalid value for "-c" / "--case": invalid choice: normal. (choose from upper, lower) 
```

Enter fullscreen mode Exit fullscreen mode

它给我们一个错误:`invalid choice: normal`，甚至指导我们从`(choose from upper, lower)`中选择选项，因为那是我们指定的`type=click.Choice(['upper', 'lower'])`。该部分的错误处理是默认处理的。

好了，现在选项被成功地传入，我们可以添加逻辑来查看我们想要的输出。

```
import click

@click.command()
@click.option('-c', '--case', type=click.Choice(['upper', 'lower']))
def hello(case):
    response = "Hello World!"
    if case == 'upper':
        click.echo(response.upper())
    elif case == 'lower':
        click.echo(response.lower())
    else:
        click.echo(response) 
```

Enter fullscreen mode Exit fullscreen mode

我们所做的就是添加一个`if-else`来检查传入的选项，并以大写、小写或普通的形式返回文本。现在，当我们再次安装并运行我们的应用程序时，我们会得到这些结果:

```
(venv) $ hello
Hello World!

(venv) $ hello -c upper
HELLO WORLD!

(venv) $ hello -c lower
hello world! 
```

Enter fullscreen mode Exit fullscreen mode

在下一篇文章中，我们将通过添加参数来进一步增强我们的命令。
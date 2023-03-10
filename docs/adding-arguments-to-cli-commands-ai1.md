# 向 CLI 命令添加参数

> 原文：<https://dev.to/wangonya/adding-arguments-to-cli-commands-ai1>

参数的工作非常类似于[选项](https://wangonya.com/blog/click-commands-options/)。如果你熟悉函数式编程，那么你就熟悉参数。这个概念在 Click 中是一样的。

让我们稍微编辑一下代码，看看如何整合参数。

```
# helloworld.py 
import click

@click.command()
@click.option('-c', '--case', type=click.Choice(['upper', 'lower']))
@click.argument('person', default='you')
def hello(case, person):
    response = "Hello World! Also, hey {} ☺️".format(person)
    if case == 'upper':
        click.echo(response.upper())
    elif case == 'lower':
        click.echo(response.lower())
    else:
        click.echo(response) 
```

Enter fullscreen mode Exit fullscreen mode

就像命令和选项一样，Click 提供了一个装饰器来添加参数。

```
@click.argument('person', default='you') 
```

Enter fullscreen mode Exit fullscreen mode

我们指定`hello()`在被调用时应该期待一个参数`person`被传入，并添加一个默认值，这样就可以在不传入参数的情况下调用命令。

保存并运行应用程序会产生以下结果:

```
(venv) $ hello  # no argument - default will be used
Hello World! Also, hey you ☺️

(venv) $ hello Meg  # Meg is the argument
Hello World! Also, hey Meg ☺️

(venv) $ hello chris -c upper   # combining arguments with options
HELLO WORLD! ALSO, HEY CHRIS ☺️ 
```

Enter fullscreen mode Exit fullscreen mode

通过选项和参数，您可以在一个命令中添加许多功能。
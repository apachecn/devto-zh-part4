# 提示用户输入

> 原文：<https://dev.to/wangonya/prompting-users-for-input-1ife>

获取用户输入是任何应用程序的重要组成部分。既然我们已经了解了[选项](https://wangonya.com/blog/click-commands-options/)，在我们的 hello world 应用程序中添加用户提示应该是轻而易举的事情。我们需要做的就是将`prompt=True`添加到 option decorator 中，这样如果没有选项传入，它就会提示用户输入。

```
# helloworld.py 
import click

@click.command()
@click.option('-c', '--case',
              type=click.Choice(['upper', 'lower']),
              prompt=True)
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

保存并运行应用:

```
(venv) $ hello
Case (upper, lower): upper
HELLO WORLD! ALSO, HEY YOU ☺️

(venv) $ hello sally
Case (upper, lower): lower
hello world! also, hey sally ☺️ 
```

Enter fullscreen mode Exit fullscreen mode

如果您愿意，您也可以设置自定义提示字符串:

```
# helloworld.py 
import click

@click.command()
@click.option('-c', '--case',
              type=click.Choice(['upper', 'lower']),
              prompt='Please enter case')
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

现在将显示设置字符串，而不是默认提示:

```
(venv) $ hello
Please enter case (upper, lower): upper
HELLO WORLD! ALSO, HEY YOU ☺️ 
```

Enter fullscreen mode Exit fullscreen mode
# 使用 Firebase 通过 CLI 应用程序执行 CRUD 操作

> 原文：<https://dev.to/wangonya/performing-crud-operations-with-a-cli-app-using-firebase-2h3e>

在本系列的下一阶段，我们将学习如何制作一个 CLI 应用程序来执行 CRUD 操作，即创建、检索、更新和删除数据。我们将使用 Firebase 来存储我们的数据。

## 我们要创造什么

具有以下功能的`contacts`应用:

```
# add a new contact with name=Peter and mobile=01034512
$ contacts add Peter --mobile 01034512
Contact Peter added!
{ 'mobile': '01034512' } 
```

Enter fullscreen mode Exit fullscreen mode

```
# view all contacts
$ contacts list
Here's a list of all your contacts:
{ ...  # list of all contacts } 
```

Enter fullscreen mode Exit fullscreen mode

```
# view single contact
$ contacts view Peter
{ 'mobile': '01034512' }

$ contacts view Meg
The contact you searched for doesn't exist 
```

Enter fullscreen mode Exit fullscreen mode

```
# update mobile number for Peter
$ contacts update Peter --mobile 00000
Contact updated!
{ 'mobile': '00000' } 
```

Enter fullscreen mode Exit fullscreen mode

```
# delete the contact Peter
$ contacts delete Peter
Contact deleted! 
```

Enter fullscreen mode Exit fullscreen mode

<small>这个帖子有点长。如果你只是想看看代码或者看看应用程序是如何工作的，那就去看看[回购](https://github.com/wangonya/contacts-cli)。</small>

## 设置 Firebase

如前所述，Firebase 将用于存储我们的数据。要设置数据库，请使用您的 google 帐户登录 [Firebase](https://firebase.google.com) 。

成功登录后，点击右上角的**转到控制台**按钮。在那里，您可以创建一个新项目。按照所需的步骤创建一个新项目。

一旦项目被创建，你会在左边看到一个**开发**侧边栏。点击**数据库**设置数据库。**云火商店**将作为第一选项显示，但这不是我们将使用的。向下滚动，你会看到一个**实时数据库**选项。点击**创建数据库**。接下来，您将看到安全选项。

[![Firebase security rules options](img/8177a44d9dac3f88530a517edc79e579.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NW18o5Cl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oxot8m2sd0u5k68byq5k.png)

因为我们不会处理认证，所以为安全规则选择**在测试模式**下开始。您的数据库现在已经创建完毕🎉

[![Empty firebase db](img/39e8d79c72c3f83e40b75d9f84e55477.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aoo7zOSH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/90prefpskmno1yxqb76k.png)

现在是设置数据库的最后一步，单击小加号 **+** 添加一个名为**联系人**的新的子节点。这将保持我们的联系(显然😅).此外，添加一个测试联系人，只是为了看看数据库将如何构造。

[![Adding a new node to hold our data](img/255c6f6aa196d5d9a67780eca7989317.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4NmGuDlH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q7o4dahnowf1h9orx6vn.png)

数据库现在应该如下所示:

[![Final result](img/0dcc9a25982d84266cd7ce91cf2ba3b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kRzFTe1G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gcupu18rh3ieh1xhdks7.png)

## App 设置

我们已经在之前的文章中介绍了设置的基本知识，所以我就不多赘述了。以下是步骤:

```
$ mkdir contacts-cli

$ cd contacts-cli

$ virtualenv env

$ . env/bin/activate

(env) $ mkdir app

(env) $ touch app/__init__.py

(env) $ touch setup.py

(env) $ touch .env 
```

Enter fullscreen mode Exit fullscreen mode

#### `.env`

这是存储 api 端点 url 的位置。Firebase [为数据库提供了一个 REST API](https://firebase.google.com/docs/reference/rest/database) ，所以这就是我们将要使用的。如果您已经按照步骤创建了数据库，只需抓取数据库顶部提供的链接，并将其设置在您的`.env`文件中。

```
export URL=https://[YOUR_PROJECT_ID].firebaseio.com 
```

Enter fullscreen mode Exit fullscreen mode

确保运行`. .env`来加载您的环境变量，否则`URL`将成为`None`。

#### `setup.py`

要了解更多关于`setup`文件的信息，你可以回到[这个](https://wangonya.com/blog/using-setuptools/)帖子。

```
from setuptools import setup, find_packages

setup(
        name="contacts",
        py_modules=find_packages(),
        install_requires=[
            "Click",
            "requests",
            ],
        entry_points="""
        [console_scripts]
        contacts=app:cli
        """,
        ) 
```

Enter fullscreen mode Exit fullscreen mode

然后在您的终端中运行`pip install --editable .`来安装应用程序。

#### `app/__init__.py`

```
import click

@click.group(invoke_without_command=True)
@click.pass_context
def cli(ctx):
    if ctx.invoked_subcommand is None:
        click.echo("Welcome to the contacts app 🥳")
        click.echo("Run contacts --help for options.") 
```

Enter fullscreen mode Exit fullscreen mode

我们需要使用`@click.group`来允许我们包含子命令。

[![command-subcommand illustration](img/30d2dd35d11d3e936a53f183f54c40a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QLLYhxvg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yprvbz79fh4i9k6oyatm.png)

如果在没有任何子命令的情况下调用主命令，将显示一条欢迎消息。主命令如何知道子命令是否已经传入？通过看上下文。`if ctx.invoked_subcommand is None`，则没有子命令被传递-显示欢迎消息。

现在一切都准备好了，所以我们可以开始工作了。

## 创建联系人

```
import os
import requests
import click

...

@cli.command()
@click.argument('name')
@click.option('--mobile', '-m', required=True)
def add(name, mobile):
    """
    Add a new contact
    """
    response = requests.put('{}/contacts/{}.json'
                            .format(os.getenv('URL'), name),
                            json={'mobile': '{}'.format(mobile)})
    click.echo('Contact {} added!'.format(name))
    click.echo(response.json()) 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到我在请求中使用了`PUT`而不是`POST`。我这样做是为了避免在 Firebase 上为我的数据生成唯一的 id。请随意使用`POST`，看看如何使用生成的 uid。

`add`命令有两个参数——一个`name`参数和一个`mobile`选项。两者都需要。运行`contacts add --help`向我们展示了我们有哪些选择。

```
(env) $ contacts add --help

Usage: contacts add [OPTIONS] NAME

  Add a new contact

Options:
  -m, --mobile TEXT  [required]
  --help             Show this message and exit. 
```

Enter fullscreen mode Exit fullscreen mode

让我们添加一个新联系人:

```
(env) $ contacts add Chris -m 34243234
Contact Chris added!
{'mobile': '34243234'} 
```

Enter fullscreen mode Exit fullscreen mode

[![Contacts database](img/501644ceab07d08cc0ff24efc0b34e24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ohYLYRV4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9tjntrzz3lnck3hcrg1l.png)

## 获取所有联系人

```
 ...

@cli.command()
def list():
    """
    View all contacts
    """
    response = requests.get('{}/contacts.json'
                            .format(os.getenv('URL')))
    click.echo('Here\'s a list of all your contacts:')
    click.echo(response.json()) 
```

Enter fullscreen mode Exit fullscreen mode

`list`命令只是在 contacts 节点上执行一个`GET`请求，并返回结果。

```
(env) $ contacts list
Here's a list of all your contacts:
{'Chris': {'mobile': '34243234'}, 'Peter': {'mobile': '8487344'}} 
```

Enter fullscreen mode Exit fullscreen mode

## 获取一个联系人

```
 ...

@cli.command()
@click.argument('name')
def view(name):
    """
    View single contact
    """
    response = requests.get('{}/contacts/{}.json'
                            .format(os.getenv('URL'), name))
    if not response.json():
        click.echo("The contact you searched for does'nt exist")
    else:
        click.echo(response.json()) 
```

Enter fullscreen mode Exit fullscreen mode

`view`命令类似于`list`，除了它接受一个`name`参数，并将其添加到 url 中，以便只返回该名称的联系人。如果数据库中不存在该名称，则返回`null`。我们在`if`块中利用这一点来返回适当的响应。

```
(env) $ contacts view Peter
{'mobile': '8487344'}

(env) $ contacts view Meg
The contact you searched for doesn't exist 
```

Enter fullscreen mode Exit fullscreen mode

## 更新联系人

```
 ...

@cli.command()
@click.argument('name')
@click.option('--mobile', '-m', required=True)
def update(name, mobile):
    """
    Update contact
    """
    response = requests.patch('{}/contacts/{}.json'
                              .format(os.getenv('URL'), name),
                              json={'mobile': '{}'.format(mobile)})
    click.echo('Contact updated!')
    click.echo(response.json()) 
```

Enter fullscreen mode Exit fullscreen mode

这与 create 命令完全一样，只是我们使用了`PATCH`而不是`PUT`(显然，这在这里没有任何区别。在一个不存在的节点上制作一个`PATCH`会创建它，就像`PUT`会做的那样。试试看。)

```
(env) $ contacts update Peter -m 552
Contact updated!
{'mobile': '552'} 
```

Enter fullscreen mode Exit fullscreen mode

## 最后，删除联系人

类似于`view`命令。我们要做的唯一改变是使用`DELETE`而不是`GET`。

```
 ...

@cli.command()
@click.argument('name')
def delete(name):
    """
    Delete contact
    """
    requests.delete('{}/contacts/{}.json'
                    .format(os.getenv('URL'), name))
    click.echo('Contact deleted!') 
```

Enter fullscreen mode Exit fullscreen mode

成功删除时返回`null`，所以我们不需要返回`response`。直接调用`request`，并在后面显示成功消息。这可以做得更好，但目前还行。

```
(env) $ contacts delete Peter
Contact deleted! 
```

Enter fullscreen mode Exit fullscreen mode

我的意思是将数据导出(写入)到本地 JSON 文件或 SQLite 数据库，但是这篇文章已经太长了😅。如果你想了解更多，这些都是有趣的尝试。

在下一篇文章中，我们将使用 pytest 向应用程序添加测试。
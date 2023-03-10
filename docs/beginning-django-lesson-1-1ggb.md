# 开始 Django -第 1 课

> 原文：<https://dev.to/jonathanfarinloye/beginning-django-lesson-1-1ggb>

## 简介

这个系列是专门为想学习 Django 的初学者准备的。基于 Python 的 Web 框架。你应该知道，Python 是一个需求，我们将使用 Python 3.0+。如果你懂一些 HTML 和 CSS 就更好了。我不是说知道所有的水平。只是基础知识。

我也在学习，所以你可能会有一些问题，我可能无法立即回答。不过，尽管提问，我会尽力回答。

因此，在本课中，我们将学习:

1.  装置
    *   [Python](#install-python)
    *   [PIP](#install-pip)
    *   [虚拟环境](#install-venv)
    *   姜戈
2.  [开始新项目](#start-project)
3.  [运行新项目](#run-project)

开始之前，您应该知道一些事情:

*   Django 项目有很棒的文档。你应该确保检查一下。
*   本教程将完全在 Linux 机器上完成，并且应该可以完美地用于 Linux 发行版和 MacOS。如果你使用的是 Windows，你可能需要做一些额外的工作。
*   终端中运行的所有命令都以 **'$'** 开头。 **'$'** 不是命令的一部分
*   如果您已经熟悉前面的章节，请随意跳到您需要的章节。

## 安装

### 巨蟒

需要安装 Python，如前所述，Python 3.0 版或更高版本。要检查 python3 是否已安装，请打开您的终端并键入

```
$ python3 
```

运行应该会打开类似如下的提示:

```
Python 3.7.3 (default, Apr  3 2019, 05:39:12) 
[GCC 8.3.0] on linux
Type "help", "copyright", "credits" or "license" for information.
>>> 
```

如果是的话，你就可以走了。如果没有，需要先安装 python3。可以去[官网](https://www.python.org/)下载安装。如果你用的是 Linux。你可以试试:

```
$ sudo apt install python3 
```

这将安装最新版本的 Python3。

### 皮普

Pip 基本上是 python 的一个包管理器。要安装它，请访问文档[此处](https://pip.pypa.io/en/latest/installing/)或尝试:

```
$ sudo apt install python3-pip 
```

### 虚拟环境

我建议使用虚拟环境，因为它允许你基本上本地化你的项目和你工作时使用的所有包。要安装 virtualenv，请打开您的终端并运行:

```
$ sudo pip3 install virtualenv 
```

或者

```
$ sudo apt install python3-venv 
```

现在我们已经安装了 virtualenv，让我们创建一个。

```
$ virtualenv -p python3 myvenv 
```

或者

```
$ python3 -m venv myvenv 
```

> 您可以用自己选择的名称替换“myvenv”

激活虚拟环境:

```
$ source myvenv/bin/activate 
```

要停用它:

```
$ deactivate 
```

> 在这一点上，我累了。也许你也是。去散散步🌞。你回来的时候我会在这里。

### 姜戈

没有吗？不走？好吧。嗯，要安装 django，首先要确保你的 virtualenv 是[活动的](#activate-venv)。然后运行:

```
$ pip install Django 
```

这会将 Django 包下载并安装到您的虚拟环境中。这就安装了我们开始所需的一切。

## 开始一个新项目

我们的项目将被命名为**牛逼**因为你很牛逼😉。你可以随意给它取另一个名字，但是不要忘了，只要你看到**很棒**，你就用你的项目名替换它。要创建，请运行:

```
$ django-admin startproject awesome 
```

还有！繁荣💥我们有一个新项目，名为真棒。

## 经营一个项目

要运行一个项目，首先进入项目的目录。

```
$ cd awesome 
```

然后运行:

```
/awesome$ python manage.py runserver 
```

你会得到这样的结果:

```
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly 
until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

June 17, 2019 - 17:41:50
Django version 2.2.1, using settings 'awesome.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C. 
```

<figure>

如果是，你做得很好。现在，打开浏览器，访问 [http://127.0.0.1:8000/](http://127.0.0.1:8000/) 。
长这样？
[![Django Install Successful Page](img/bf7d4f19c4900a25f1315916e279e5bb.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--NbCkA3R6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/52jtd3hld5foka1z6gps.png)

<figcaption>Your project is up and running!</figcaption>

</figure>

太好了！我希望我没有让你困惑。如果有什么我应该知道或能帮上忙的，请留言。
<small>项目可以在这里找到</small>
<small>封面图片由</small>

#### 欢呼✨✨
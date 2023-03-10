# Flask REST API:项目基础

> 原文：<https://dev.to/dbanty/python-rest-api-project-foundations-f61>

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

在这篇文章的最后，你将看到我如何建立一个 Python 项目的基础，并阅读我对基础技术和技巧的一些论证。我写这篇文章的目的是让你通过测试了解一个基本的跑步烧瓶应用程序，但是我正在度假，时间不多了，所以这将在下一篇文章中介绍。以下是我将介绍的内容:

1.  休息
2.  计算机编程语言
3.  pyenv
4.  皮查姆
5.  项目结构
6.  诗意
7.  瓶

## 休息

创建 web API 有多种技术。我不会试图涵盖所有不同方法的利弊，你可以写一整本书进行比较和对比。我在创建 API 时使用 REST 有两个主要原因:

1.  因为它是一个概念，而不是一项技术，所以你可以用任何编程语言来消费和生产 REST APIs。
2.  这是非常成熟的，意味着有大量好的工具可用

## 巨蟒

使用 Python(或不使用 Python)的理由有很多。它们可以在互联网上找到，比我在这里要详细得多。为了信守我解释每一个设计决策的承诺，以下是我喜欢它的几个方面的快速列表:

*   经过解释，它非常灵活，跨平台，并且非常容易调试
*   有一个成熟的 web 框架生态系统
*   几乎所有的东西都有很多包
*   它很受数据科学家的欢迎，这对于那些想要使用您的 API 的人来说可能会很方便。
*   我用起来超级舒服，因为我用它来工作

## pyenv

大多数严肃的 Python 开发人员必须在他们的开发机器上一次管理多个版本的 Python。如果你正在使用诗歌(我就是这样做的)，你需要一些工具来做到这一点。我使用 macOS，我发现在那个操作系统上做这件事的最好方法是使用一个叫 pyenv 的工具。开始真的很容易:

1.  安装自制软件:`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`
2.  安装 pyenv: `brew install pyenv`
3.  启用垫片:`echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n eval "$(pyenv init -)"\nfi' >> ~/.bash_profile`
4.  重启你的外壳
5.  安装所需版本的 Python `pyenv install 3.7.3`

## PyCharm

这是另一个有争议的主观话题:编辑。我用的是 JetBrains 的 PyCharm。当我第一次开始写 Python 的时候，我尝试了很多不同的编辑器，这是我最喜欢的一个。它有很多特性，其中一些我稍后会向您展示。如果您想尝试一下，[有一个免费版本](https://www.jetbrains.com/pycharm/)，否则只需跳过我提到的 PyCharm 特定的部分，或者为您选择的编辑器找到等效的版本。

## 项目结构

这是一个我认为谈得不够多的话题，项目结构！大多数 Python 教程只是让你把一堆代码放在一个文件夹中并运行它。这对于让某人尽可能快地编写可用的代码来说是很棒的，但是这是不可持续的。我把我所有的 Python 项目，不管它们的目的如何，都组织在我所谓的“包结构”中。它看起来有点像这样:

```
python-rest
|-- README.md
|-- pyproject.toml
|-- python_rest
|   `-- __init__.py
`-- tests
    `-- conftest.py 
```

Enter fullscreen mode Exit fullscreen mode

这里的“包名”是“python-rest”。应用程序的所有逻辑(运行的实际代码)都在模块“python_rest”中。项目元数据放在包的根目录下。测试放在测试目录中。注意，包名通常用连字符分隔，模块名是 snake_case。不是每个人都遵循这个惯例，但这很常见，所以我学会了。以下是对所有项目使用包结构的一些好处:

*   它将业务逻辑与其他一切分离开来
*   它允许您轻松地将您的项目构建成某种用于发布/部署的捆绑格式(例如 wheel)。
*   因为所有的东西都在一个合适的包中，所以你总是可以使用相对导入和合适的名称间距(如果你不一致的话，围绕这些东西的问题总是会出现)。

### 诗歌

我使用诗歌的所有理由已经打包在一个关于 Python 包管理的系列文章中，如果你还没有看的话可以看看。

[![dbanty](img/a8d85eaa5d0f2a21db38db8784d15462.png)](/dbanty) [## 富有诗意的 Python

### 迪伦·安东尼 1918 年 5 月 18 日 3 分钟阅读

#python #productivity #distribution #tutorial](/dbanty/productive-python-with-poetry-67h)

要开始学习诗歌:

1.  安装它:`curl -sSL https://raw.githubusercontent.com/sdispater/poetry/master/get-poetry.py | python`
2.  重启你的外壳
3.  转到您的项目目录
4.  给自己创造一个`pyproject.toml` : `poetry init`
    1.  你会被问到一些问题，只是填写它们
5.  设置 pyenv 以使用正确的 Python 版本:`pyenv local 3.7.3`
6.  配置诗把你的虚拟环境放到项目目录:`poetry config settings.virtualenvs.in-project true`
7.  创建您的虚拟环境:`poetry install`
8.  告诉 PyCharm 使用虚拟环境
    1.  打开偏好设置
    2.  转到项目>项目解释器
    3.  点击右上角的小齿轮，然后添加
    4.  选择虚拟环境>现有环境
    5.  它通常会自动选择。venv 文件夹。如果没有，就要浏览/。venv/bin/python

搞定了。您已经设置了一个虚拟环境，并准备好开始添加依赖项。

## 烧瓶

Flask 是最流行的 Python web 框架之一(我认为它现在仅次于 Django 排名第二)。它的设计理念是拥有一个相对较小的内核和一个庞大的扩展库，以添加您想要的任何功能。这不是最容易上手的东西，但是非常强大。

1.  将 flask 添加到您的项目:`poetry add flask`

您会注意到，poem 将它安装到您的虚拟环境中，将其作为依赖项添加到您的`pyproject.toml`文件中，并在`poetry.lock`文件中记录您正在使用的库的特定版本和散列。

> 抱歉，这篇文章还没有为你提供任何可以运行的内容。正如我开始所说，我没有时间了。我会努力让下一个帖子更快，这样你就可以看到 Flask 实际上是如何工作的！
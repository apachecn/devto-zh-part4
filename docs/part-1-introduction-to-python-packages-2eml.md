# 第 1 部分 Python 包介绍

> 原文：<https://dev.to/thefern/part-1-introduction-to-python-packages-2eml>

这将是本系列的第一篇文章，我打算向您展示 Python 打包的所有内容。也许不是一切，但你需要知道的关于创建一个自包含模块的一切，你可以部署到 Python 打包索引 aka [PyPi](https://pypi.org/) 和 Github 供世界使用。

## 到今天为止的系列主题列表

*   Python 包简介
*   基本包装
*   Cookiecutter 的另一个包
*   Argparse 和脚本入口点
*   打包和部署

## 为什么你需要一个包？

如果你是一名程序员，并且有 99%的可能性，因为你正在阅读这篇文章。总有一天你的脑海里会突然灵光一现。你有一个绝妙的 python 项目的想法。更好的是，你认为这将有利于社区或特定群体。它可以是一个工具，助手功能，甚至是一个小脚本。无论想法是什么，发布一个包可以确保你的作品可以被其他人使用，甚至是将来你的计算机发生问题时。(是的，我知道那是监视器，哈哈)

[![alt text](img/48b413cc3d27799778e4b9da005025c7.png)](https://i.giphy.com/media/S7u66urzxc2J2/giphy.gif)

## 使用软件包管理器的好处

Python 的包安装程序(pip)并不是唯一的包管理器。还有其他你可能用过的管理器，homebrew，apt-get，rpm，等等。

我们将集中讨论 pip，因为它是围绕 python 构建的，是跨平台的，并且有很好的文档记录。此外，您可以轻松地安装其他 pip 包，以及项目的依赖项。您可以创建多平台包。您可以通过简单的命令轻松安装/更新。

## 一个包经理实际上是做什么的？

包管理器试图解决一个问题，即让用户下载模块变得容易和容易，同时通过提供部署所需的工具来帮助程序员以合理的方式构建包。想象一下，如果每次有人需要打印一份报表，他们必须上网下载一些很棒的打印模块，然后复制并粘贴到指定的文件夹中。如果打印模块有了新版本，会发生什么情况，现在您必须冲洗并重复。

[![](img/54fb3a708ec3c8eb5a7c067a7771caa5.png)](https://i.giphy.com/media/3nFKBN97bDwli/giphy.gif)

直到你不得不维护一个包的时候，你才会想到维护一个包需要做什么。更新回购、版本、贡献者列表、可分发内容等。

pip 的基本前提是它是一个完整的 cmd 实用程序。

*   pip 安装一些软件包
*   pip 搜索一些 _ 包
*   pip 安装一些软件包-升级
*   pip 卸载一些 _ 包
*   pip 安装 git+[https://github.com/some\_repo/a\_repo.git](https://github.com/some%5C_repo/a%5C_repo.git)
*   pip freeze > requirements.txt(将已安装软件包的列表发送到文本文件中)
*   pip install -r requirements.txt(其中 requirements 是每行一个包，在构建新的 virtualenv 时非常棒)

## 下一篇

好的，这篇文章中有很多有用的信息。在下一篇文章中，我们将设置我们的开发环境，并创建一个基本包。感谢阅读！
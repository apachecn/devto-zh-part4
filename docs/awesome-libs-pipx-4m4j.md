# 令人敬畏的 Libs: pipx

> 原文：<https://dev.to/humrochagf/awesome-libs-pipx-4m4j>

(最初发布在[我的博客](https://humberto.io/blog/awesome-libs-pipx/)上)

我开始这一系列的文章是为了给你一些关于库的技巧，这些技巧对你作为一名开发人员的日常工作会很有帮助，同时也介绍一些我认为你应该关注的库。

一个好的开发人员的额外津贴之一是在你的腰带上有一套合适的工具，没有什么比一个安装其他库的库更适合开始这个系列了！

你在 Python virtualenv 中安装了多少次 Python CLI 工具？你有没有更新过一些工具依赖关系，如果让另一个库停止工作，因为它依赖于一个公共的依赖关系？

如果你喜欢组织、稳定和不重复自己，pipx 对你来说是一个令人敬畏的自由。

[![organized kittens image](img/a7bebc36c68a46dba5fd4f1301717420.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iJFyvnWr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qtnubkw6jwf6t38r1czm.jpg)

Pipx 是一个方便的工具，可以将您的 Python 程序隔离到不同的环境中，公开它们的入口点，这样您就可以从任何地方运行它们。

您还可以轻松地升级已安装的软件包，而不会在依赖项之间产生冲突，并在您的系统上一次性安装一些有用的工具，如 [nox](https://nox.thea.codes/en/stable/) 、 [flake8](http://flake8.pycqa.org/en/latest/) 和 [black](https://black.readthedocs.io/en/stable/) (无需在您创建的每个 virtualenv 上安装)。

要安装 pipx，您必须运行:

```
$  python3 -m pip install --user pipx
$  python3 -m pipx ensurepath 
```

Enter fullscreen mode Exit fullscreen mode

第二个命令是确保您将拥有 CLI 访问权来运行随 pipx 安装的程序。

现在，您可以在孤立的环境中安装您最喜欢的 Python 工具，并从中获得乐趣

```
$  pipx install pyjokes
$  pyjoke
There are two ways to write error-free programs;  only the third one works. 
```

Enter fullscreen mode Exit fullscreen mode
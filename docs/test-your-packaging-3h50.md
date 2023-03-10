# 测试您的包装

> 原文：<https://dev.to/mblayman/test-your-packaging-3h50>

软件开发是一个充满错误的过程。我们通常称之为 bug，但这是为了让开发人员不会因为搞砸了而感到难过。说“哦，那只是一个错误”比说“哦，那是一个错误”要容易得多

不久前我犯了一个错误。事实上，这是一个异乎寻常的错误。我为 MarkWiki 发布的软件包装破损。这意味着 ***任何试图安装*** 的人都会被彻底破解。哎呀。正因为如此，我可能失去了许多愿意安装它的人的信任。根据下载统计，超过 300 万人可能立即发现了我的错误，因为 MarkWiki 在他们试图运行时崩溃了。

这个错误是完全可以避免的。我所需要做的就是对一个已安装的版本运行测试套件，这个错误会像对待其他 300 个人一样给我当头一棒。

当有人友好地通过 GitHub 指出这个错误时，我决定我应该修复这个问题以避免将来出现这样的尴尬。这个包装问题的解决方案来自于[特拉维斯·CI](https://travis-ci.org/)、[托克斯](http://tox.readthedocs.org/en/latest/)和[虚拟人](http://virtualenv.readthedocs.org/en/latest/)。Travis 是一种使用“持续集成”进行自动化构建的服务这意味着每一个对 GitHub 的源代码提交都会触发 Travis 构建 MarkWiki 并运行测试。使用 virtualenv 在虚拟环境中运行自动化构建，这样每个构建都是隔离的(即，来自一个构建的内容不会泄漏到另一个构建中并影响构建过程)。最后我用了毒理。Tox 很简洁，因为它被设计用来针对*安装的*包进行测试(听起来很适合我，对吧？).

这个过程看起来像这样:

1.  我为 MarkWiki 完成一个功能，推给 GitHub。
2.  GitHub 告诉 Travis 这个变化。
3.  Travis 创建了一个虚拟环境并调用了 Tox。
4.  Tox 构建一个 MarkWiki 包，下载所有的依赖项，并运行测试。
5.  如果出了什么问题，特拉维斯会发邮件给我。

我的存储库中的配置非常简单。Travis CI 团队有关于如何连接 GitHub 和他们的服务的很好的文档。

我需要向我的存储库中添加两个文件: **.travis.yml** 和 **tox.ini** 。下面列出了所有文件。一、特拉维斯档案:

```
language: python
env:
  - TOX_ENV=py27
  - TOX_ENV=py26
install:
  - pip install tox
script: tox -e $TOX_ENV 
```

二、Tox 文件:

```
[tox]
envlist = py26, py27

[testenv]
deps = nose
       coverage
commands = nosetests --with-coverage --cover-package=markwiki 
```

设置很简单，未来的尴尬最小化。所以，**测试你的包吧！**

这篇文章最初发表在 mattlayman.com 的上。
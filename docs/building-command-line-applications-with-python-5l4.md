# 用 Python 构建命令行应用程序

> 原文：<https://dev.to/wangonya/building-command-line-applications-with-python-5l4>

你可以用 Python 做很多令人惊奇的事情。当我去年开始自学 Python 时，我发现最有趣的事情之一是创建 CLI 应用程序是多么容易。因为我在实践中学习得最好，所以我在其他应用程序中创建了 [norris-cli](https://github.com/wangonya/norris-cli) ,并发现了无限的可能性😄。

构建这类项目是一种很好的学习方式，而且非常有趣。为了提高我的技能并帮助其他人学习，我将做一个关于“用 Python 构建 CLI 应用程序”的系列文章。我会从基础开始，一步一步来。最后，我们将通过创建一个练习项目 <sup>*</sup> 来将我们所学的内容付诸实践。当然，我们做出来的东西不会很有用，但我们会在这个过程中学到足够的东西，从而做出真正有用的东西。

### 我要讲的内容:

*   设置(Python 3.x、pip、Virtualenv、Click 等)
*   命令、选项和参数
*   使用 Setuptools
*   提示用户输入
*   CRUD(带有在线 API) **(实践项目)**
    *   使用数据结构
    *   写入本地文件
*   使用 pytest 进行测试
*   在 [Pypi](https://pypi.org/) 上发布包裹

我希望有经验的人和刚开始接触 Python 的人都会发现这个系列很有用。

#### 更新

我最初打算做两个实践项目，但这似乎是多余的，所以我决定把 CRUD 部分作为项目。
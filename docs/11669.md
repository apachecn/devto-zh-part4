# Python pip，装了什么？

> 原文：<https://dev.to/petercour/python-pip-what-s-installed-2dib>

如果您正在编写 Python 程序，您可能会使用 pip 数千次。 [pip 是](https://pypi.org/project/pip/) [Python](https://pythonbasics.org) 的包安装器。您可以使用 pip 从 Python 包索引和其他索引安装包。

当创建一个 Python 项目时，你是在一个虚拟环境中这样做的(也可能不是？).

[![](img/fd0a9014276209abefe7ae03eda8aa90.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fX7N5YFj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252FWwIe4phY9crpm%252Fgiphy.gif%26f%3D1)

```
pip install your_module 
```

然后，您为您的项目安装软件包。那么你怎么知道所有安装的模块是什么呢？

简单，这是命令

```
pip list 
```

所以如果你想知道你有哪个版本的模块，结合 grep。

```
λ  ~  pip list | grep 'tensorflow'
tensorflow (1.14.0)
tensorflow-estimator (1.14.0) 
```

如果你使用的是 Ubuntu Linux 或者类似的系统，你可能会遇到同时安装了 pip、pip3、python 和 python3 的奇怪情况。Ubuntu 的创造者有什么邪恶的计划吗？

[![](img/abcaa6899024bd829453ad7f7231f539.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8KSt49Dq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252Fl0HluOGg5nXebqJwI%252Fgiphy.gif%26f%3D1)

在这种情况下，只需在 pip 命令后添加 3。其他操作系统，用 Python 和 pip 就可以了。

相关链接:

*   [Python pip，包管理器](https://pypi.org/project/pip/)
*   [Python 包库](https://pypi.org/)
*   [学习 Python](https://pythonbasics.org)
*   [Python 练习](https://pythonbasics.org/exercises/)
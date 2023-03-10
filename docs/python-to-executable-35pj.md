# Python 到可执行文件

> 原文：<https://dev.to/petercour/python-to-executable-35pj>

[![](img/c04947ad88f122c0bfb03c5a86f3e53a.png)](https://i.giphy.com/media/349qKnoIBHK1i/giphy.gif)

可以把 Python 转换成可执行程序(exe，elf，dmg)。然后，这些程序可以像用户计算机上的任何其他程序一样运行。

这很棒，因为你不想解释“安装 python，打开命令行，输入这个命令”，而是共享一个一键程序。

## 认识 Pyinstaller

为此，pyinstaller 是一个选项。

```
pip install pyinstaller 
```

那就创建你的程序吧

```
pyinstaller --onefile hello.py 
```

您的程序将在 dist 目录中可用。您将获得适用于您当前使用的操作系统的可分发程序。

在 windows 上你会得到一个 exe 文件，在 mac 上是一个 dmg 文件，在 linux 上是一个 elf 文件。在 hello world 的情况下，您仍然需要从命令行运行它；)

学 Python？

*   [https://pythonbasics.org](https://pythonbasics.org)
*   [https://pythonprogramminglanguage.com](https://pythonprogramminglanguage.com)
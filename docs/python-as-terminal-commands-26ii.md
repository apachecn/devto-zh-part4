# Python 作为终端命令

> 原文：<https://dev.to/petercour/python-as-terminal-commands-26ii>

创建脚本时经常使用 Python。但是您知道您可以直接从命令行运行它吗？

不，我不是说从命令中运行一个. py 文件，而是运行一个程序输入。

### 什么是 Python？

Python 是一种编程语言。

如果你不知道 [Python](https://pythonprogramminglanguage.com/) 编程语言，下面的代码就没有意义。您应该熟悉 for 循环的概念。

### Python 代码作为命令

您可以使用命令行作为输入在一行中运行 Python，如下所示:

```
python -c "for i in range(1,10): print(i)" 
```

然后输出数字。
现在，您可以使用 alias 命令创建运行该 python 命令的自定义命令

```
alias forloop='python -c "for i in range(1,10): print(i)"' 
```

然后在你的终端

```
# forloop 
```

[![](img/134780edd15d700d6cbb9902c66f2bb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hrXZlP4H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252F3oKIPnAiaMCws8nOsE%252Fgiphy.gif%26f%3D1)

您可以将这些命令存储在您的。bashrc 文件。

这是个好主意吗？从安全角度来看，可能不会。
参见[在 Python 进程中注入代码](https://dev.to/petercour/inject-code-in-python-process-3cfh)。
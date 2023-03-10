# 我的基本 Windows 设置

> 原文：<https://dev.to/feregri_no/my-basic-windows-setup-4gdh>

尽管最近我使用了越来越多的 Linux 平台，但我的一部分仍然绑定在 Visual Studio 和 Windows 上；然而，我不会让这阻止我以几乎和我在 Ubuntu 分区中一样的方式进行 Python 开发。

这就是为什么我开始为那些对在 Windows 中使用 Python 进行一定程度的控制感兴趣的人创建这个简单的指南。另外，我确定下次格式化我的电脑时我会忘记怎么做，所以这也是为了未来的我。

## 巧克力

Chocolatey 是*“一个用于 Windows 的包管理器(像 apt-get 或 yum，但用于 Windows)”*，我将用它来安装`git`和`make`。我使用 Git 进行版本控制，让*有些繁琐的任务“自动化”。*

 *要安装 Chocolatey，请使用管理权限启动 PowerShell 并粘贴以下命令:

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1')) 
```

Enter fullscreen mode Exit fullscreen mode

警惕所有要求你粘贴到 PS 终端的命令！请在处仔细检查[命令。](https://chocolatey.org/docs/installation#install-with-powershellexe)

### 安装工具

现在，一旦安装了 Chocolatey，关闭 PowerShell 并以管理权限再次启动它。只需粘贴以下命令来安装`make`和`git` :

```
choco upgrade make git -y 
```

Enter fullscreen mode Exit fullscreen mode

中间的`-y`是因为我不想让*乔科*向我确认...我知道我在做什么。

一旦安装完成，您应该能够在 PS 中键入`make`，并看到类似这样的内容:

```
make: *** No targets specified and no makefile found.  Stop. 
```

Enter fullscreen mode Exit fullscreen mode

## 巨蟒

要安装 Python，你必须前往 [Python 下载](https://www.python.org/downloads/)，你可以选择任何你想要的版本；我选择了 [Python 3.6.8](%5Bhttps://www.python.org/downloads/release/python-368/%5D(https://www.python.org/downloads/release/python-368/)) ，我下载了 Windows x86-64 可执行安装程序。

执行后，安装程序将向您显示如下窗口:

[![https://i.imgur.com/Isdjhzd.png](img/055677bd96ae9564892a40956b1422e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fpSAGdA4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Isdjhzd.png)

在这种情况下，我选择了 *Install Now* ，并确保记下了那里显示的路径。安装完成后，我必须编辑环境变量，使它们指向 Python 的安装位置，为此需要修改`Path`环境变量，如下所示:

[![enter image description here](img/89347f99f2d34be85f5fcdefba9bc9b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c8reUa3s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/1WstUpH.png)

新路径是:

*   `%USERPROFILE%\AppData\Local\Programs\Python\Python36`
*   `%USERPROFILE%\AppData\Local\Programs\Python\Python36\Scripts`

无论如何，您应该确保以`WindowsApps`结尾的路径在新的 Python 路径之下。

现在，您应该能够从终端(或 PowerShell)使用 Python，键入`python`并看到类似于此输出的内容:

```
Python 3.6.8 (tags/v3.6.8:3c6b436a57, Dec 24 2018, 00:16:47) [MSC v.1916 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information. 
```

Enter fullscreen mode Exit fullscreen mode

### `pipenv`

一旦你安装了 Python，就几乎可以开始编码了，但是为了保持整洁的安装，并且隔离我们的每个不同的项目，建议使用虚拟环境管理器，这就是 *Pipenv* 的用途。要安装它，在命令行中粘贴以下命令:

```
pip install --user pipenv 
```

Enter fullscreen mode Exit fullscreen mode

这将安装`pipenv`，但我们离能够使用它还有一步之遥。由于我们使用了`--user`标志，这个包被安装在一个不同的位置，我们需要将它添加到我们的路径中，这与我们在安装 Python 时必须做的事情非常相似。

要添加的路径是:

*   `%USERPROFILE%\AppData\Roaming\Python\Python36`
*   `%USERPROFILE%\AppData\Roaming\Python\Python36\Scripts`

确保它们在顶部，甚至在原始 Python 路径之后。

## 附加工具

我建议安装其他工具，但这些工具对您来说可能不太重要:

*   [Visual Studio](https://visualstudio.microsoft.com/)
*   [Visual Studio 代码](https://code.visualstudio.com/)
*   [记事本++](https://notepad-plus-plus.org/)
*   mobaxterm
*   [提琴手](https://www.telerik.com/fiddler)
*   [7-zip](https://www.7-zip.org/download.html)

我希望这篇文章对你有所帮助，如果你有任何问题或疑问，可以在 Twitter 上问我。*
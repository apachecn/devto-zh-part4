# 设置 python 的 Windows 嵌入式发行版(正确)

> 原文：<https://dev.to/fpim/setting-up-python-s-windows-embeddable-distribution-properly-1081>

## 可嵌入分布

作为一个 python 主义者，如果说 Windows 有我喜欢的地方，那一定是你可以使用 python 的[嵌入式分发](https://docs.python.org/3/using/windows.html#windows-embeddable)。

> 嵌入式发行版是一个包含最小 Python 环境的 ZIP 文件。它旨在作为另一个应用程序的一部分，而不是由最终用户直接访问。

在我看来，这是一个便携的，随时可以出货的虚拟。然而，嵌入式发行版有一些限制:

> 第三方软件包应该由应用程序安装程序与嵌入式发行版一起安装。本发行版不支持使用 pip 来管理常规 Python 安装的依赖项，尽管在一定程度上可以包含和使用 pip 来进行自动更新。一般来说，第三方软件包应被视为应用程序的一部分(“供应商”)，以便开发人员可以在向用户提供更新之前确保与新版本的兼容性。

听起来很可怕，对吧？它说它甚至不支持 pip。不要担心，按照这些简单的步骤，您将拥有一个完全可行的嵌入式环境。

### 得到分布

1.  前往[https://www.python.org/downloads/windows/](https://www.python.org/downloads/windows/)
2.  选择你喜欢的版本 python，下载对应的`Windows x86-64 embeddable zip file`。
3.  解压文件。

为了让本教程更容易理解，我假设你已经下载了`Python3.7`并解压到`C:\python\`。

### 获取 pip

发行版没有安装 pip，您需要自己安装:1。在[https://bootstrap.pypa.io/get-pip.py](https://bootstrap.pypa.io/get-pip.py)2 下载`get-pip.py`。保存到`c:\python\get-pip.py` 3。在命令行运行`C:\python\python get-pip.py` 4。pip 现已安装

### 配置路径

这个发行版的运行时在`sys.path`中没有添加空字符串`''`，所以`current directory`没有添加到`sys.path`中，要解决这个问题，你需要:

1.  打开`C:\python\python37._pth`。
2.  取消对行`#import site`的注释并保存。
3.  创建新的。py 文件并另存为`c:\python\sitecustomize.py`:

```
import sys
sys.path.insert(0, '') 
```

Enter fullscreen mode Exit fullscreen mode

### lib2to3 期

当你尝试安装一些软件包时，你会遇到以下错误:

```
error: [Errno 0] Error: 'lib2to3\\Grammar3.6.5.final.0.pickle' 
```

Enter fullscreen mode Exit fullscreen mode

1.  将`C:\python\python37.zip`解压到一个`new folder`
2.  删除`C:\python\python37.zip`
3.  将`new folder`重命名为`python37.zip`(是的，一个名为`python37.zip`的新文件夹)

Python 的导入模块能够将 zip 文件视为文件夹，但是它不能读取 zip 文件中的 pickle 文件，所以将其解压缩并重命名。

### 运行画中画

如果您不想弄乱您的路径，您可以简单地在您的窗口命令提示符中执行以下操作:

1.  `CD C:\python\Scripts`
2.  `pip install xxxxx`

### 运行脚本

同样，如果你不想弄乱你的路径，你可以简单地在你的窗口命令行提示:

1.  `C:\python\python <path to your script>`

### 搞定！
# 更新 Windows 中的所有 PyPI

> 原文：<https://dev.to/bugmagnet/updating-everything-pypi-in-windows-4ddi>

我有几个软件的集合:基于 [npm](https://www.npmjs.com/) 的， [scoop](https://scoop.sh/) 的， [pip](https://pypi.org/project/pip/) 的，和 [cygwin64](https://www.cygwin.com/) 的。我还使用 [FileHippo 应用管理器](https://filehippo.com/download_app_manager/)来更新应用程序，并不时使用 [Ninite](https://ninite.com/) 。

我试图找到一种方法来更新我的 PyPI 收藏。下面是基于 Linux 的类似产品的三种适用于 Windows 的方法:

*   使用`pip`、`sed`和`xargs`(通过`scoop`可用)

```
pip freeze | sed -e "s/==.*//" | xargs pip install -U 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们通过管道`pip freeze`到`sed`用一个空字符串替换双等号和其后的所有内容，然后通过管道把它传递给运行`pip`的 xargs。

*   以更像`CMD`的方式使用`pip`和`sed`

```
FOR /F %f IN ('pip freeze ^| sed -e "s/==.*//"') DO pip install -U %f 
```

Enter fullscreen mode Exit fullscreen mode

在这种方法中，我们在`for`上使用文件集参数(/F)。单引号之间的命令被求值(它执行#1 的 pip 和 sed 操作),然后每一行都被放入`%f`元变量中，以便在`do`子句中执行。

*   仅使用`pip`和`CMD`。

```
FOR /F "tokens=1 delims==" %f IN ('pip freeze') DO pip install -U %f 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们使用了`for /f`方法，但这次给出了额外的参数来控制对来自`pip freeze`的输出的解析:我们在`=`上分裂，并获取第一个令牌。每个结果进入`%f`，由`do`子句进行处理。

在 Windows 上还有其他方法可以做到这一点，比如 PowerShell。希望有人能示范一下。
# 在脚本中检测 Python 版本

> 原文：<https://dev.to/tonetheman/detecting-python-version-in-a-script-2c5n>

最近，我需要知道我运行的程序是使用 Python2 还是 Python3。

在 Python 解释器中有几种方法可以做到这一点。

最隐晦的方式(IMO)可能是最可靠的

```
import sys
major_version = sys.version_info[0]
print(major_version) 
```

Enter fullscreen mode Exit fullscreen mode

这个方法是可靠的，但是我怎么知道要查看 sys.version_info 中的位置 0 呢？这看起来很奇怪，感觉像魔术。

在 Python 2.6 中，sys.version_info 是一个元组。它不是一个类，所以 sys.version_info[0]是主要编号(也称为 2)。在 Python 2.7 和更高版本中，sys.version_info 实际上变成了一个类，这种类型的代码变得有效

```
print(sys.version_info.major) 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码在旧的 Python 中会失败，所以检查你的版本的安全方法是丑陋的方法:(
# 第 4 部分 Argparse 和脚本入口点

> 原文：<https://dev.to/thefern/argparse-and-script-entry-points-4cde>

我们准备从 cmd 线路调用我们的实用程序，问题是我们还没有设置一个入口点。我们想从 cmd 行做这样的事情`my_cli args`。

一旦我们完成了这些，我们将继续设置 argparse，这是一个在处理输入参数时减轻痛苦的模块。还有其他模块，如 click 和 optparse。Argparse 是我在 cmd 实用程序中使用最多的一个。

参数模块抽象了一些东西，使处理参数变得更容易，它还根据参数设置添加了一个快速帮助命令。您不必担心调用错误的参数，argparse 会处理得很好。

## 设置脚本入口点

[入口点的文档](https://packaging.python.org/specifications/entry-points/)。您可以有多个入口点，但我们将只使用一个。

在`setup.py`的最后:

```
...
version='0.0.1',
    zip_safe=False, 
```

Enter fullscreen mode Exit fullscreen mode

添加以下内容:

```
entry_points={
        'console_scripts': [
            # command = package.module:function
            'my_cli = another_package.another_package:say_hello',
        ],
    }, 
```

Enter fullscreen mode Exit fullscreen mode

## 快速测试

此时，我们可以卸载当前的实用程序，因为我们已经修改了`setup.py` :

```
pip3 uninstall another-package
cd git/another-package
python3 setup.py develop 
```

Enter fullscreen mode Exit fullscreen mode

如果安装成功，进入终端或 cmd 提示符并键入`my_cli`，您将得到下面的错误。`say_hello`需要一个字符串的参数，所以我们得到了一个应该得到的回溯。有了这个，我们知道我们的入口点正在发挥它应有的作用，尽管我们不能在不提供参数的情况下调用`say_hello`。请注意，即使当我试图提供我的名字，该工具不知道如何处理该参数，担心没有 argparse 来拯救。

```
Fernandos-MacBook-Air:another_package terminalX$ my_cli "fer"
Traceback (most recent call last):
  File "/Library/Frameworks/Python.framework/Versions/3.7/bin/my_cli", line 11, in <module>
    load_entry_point('another-package', 'console_scripts', 'my_cli')()
TypeError: say_hello() missing 1 required positional argument: 'name' 
```

Enter fullscreen mode Exit fullscreen mode

## Argparse 设置

修改`another_package.py`，我不认为`__ main __`是必要的，因为我们将从我们的入口点扩展`main`，但我总是这样设置我的工具。这样你也可以调用 python `another_package.py args*`。

```
# -*- coding: utf-8 -*-

import argparse

"""Main module."""
def say_hello(name):
    print("hello %s" % (name))

def main():
        parser = argparse.ArgumentParser()
        parser.add_argument('-n', '--name', action="store", help='Provides name')
        args = parser.parse_args()

        say_hello(args.name)

if __name__ == ' __main__':
          main() 
```

Enter fullscreen mode Exit fullscreen mode

修改`setup.py` :

```
'my_cli = another_package.another_package:main', 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们已经准备好进行另一个测试，卸载和重新安装，如快速测试部分所述。

## 第二次测试

安装实用程序后，运行以下命令:

```
my_cli
my_cli -h
my_cli -n yourname 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，您应该会看到您的名字正确输出。如果你想了解更多关于 argparse 的知识，请查看文档。

```
Fernandos-MacBook-Air:another_package terminalX$ my_cli -n fer
hello fer 
```

Enter fullscreen mode Exit fullscreen mode

## 下一篇

在下一篇文章中，我们将把我们的包发布到 github 或任何 git repo 上。记住在 github 中单独有一个包是很好的，因为你可以用 pip 安装。感谢阅读，希望你喜欢，直到下一个。
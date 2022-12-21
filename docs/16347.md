# 在虚拟环境中运行空闲(Python IDE)

> 原文：<https://dev.to/wincentbalin/run-idle-python-ide-in-virtual-environment-35c3>

想象一下:你正在运行用 Python 实现的软件，有一个问题你想调试或编辑掉。软件驻留在虚拟环境中，除了该虚拟环境和标准 Python 安装之外，不安装任何其他东西(或者不允许安装)。你该怎么办？

你可以在激活的虚拟环境中运行 [IDLE](https://docs.python.org/3/library/idle.html) 命令:

```
python -m idlelib.idle 
```

Enter fullscreen mode Exit fullscreen mode

该命令使用 Python 提示符打开 IDLE 的启动窗口。从那里你可以打开你想要编辑的文件。

但是如果您立即打开 Python 文件会怎么样呢？然后使用该命令:

```
python -m idlelib.idle filename 
```

Enter fullscreen mode Exit fullscreen mode

虽然 IDLE 没有 PyCharm 的所有优点，但它比 Notepad.exe 好，几乎总是被安装并具有调试功能。你甚至可能会喜欢它。

来源:[从批处理文件](https://gist.github.com/wincentbalin/411d89dea8a0a017eb4068cf5a007b2e)运行空闲
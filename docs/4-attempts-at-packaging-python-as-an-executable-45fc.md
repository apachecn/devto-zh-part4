# 4 次尝试将 Python 打包成可执行文件

> 原文：<https://dev.to/tryexceptpass/4-attempts-at-packaging-python-as-an-executable-45fc>

几年前，我研究了如何创建 Python 应用程序的单文件可执行文件。当时的目标是制作一个桌面界面，在一个包中包含其他文件和二进制文件。使用 PyInstaller，我构建了一个可以跨平台执行的二进制文件，看起来就像任何其他应用程序一样。

快进到今天，我也有类似的需求，但使用情形不同。我想在 Docker 容器中运行 Python 代码，但是容器映像不需要 Python 安装。

我决定调查更多的替代方案，并在此进行讨论，而不是盲目重复我上次尝试的方法。

[读下去...](https://tryexceptpass.org/article/package-python-as-executable/)
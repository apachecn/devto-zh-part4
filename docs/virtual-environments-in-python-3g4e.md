# Python 中的虚拟环境

> 原文：<https://dev.to/petercour/virtual-environments-in-python-3g4e>

[![](img/b5264180bfa7206c2c1dbc2e3a1942f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UXJxMRmd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttp%253A%252F%252Fimages5.fanpop.com%252Fimage%252Fphotos%252F30500000%252FPandora-s-Box-silent-movies-30540095-500-340.gif%26f%3D1)

Python **在系统范围内安装模块**。这与其他需要导入模块的编程语言不同。

系统范围的模块安装会产生冲突，你需要虚拟环境。

### 在 Ubuntu Linux 上

在 Ubuntu 18.x 上设置虚拟环境
:

```
sudo apt-get install python3-venv 
```

在其他系统上，您需要另一个命令来安装。很有可能默认安装在 Mac 和 Windows 上。

### 创建虚拟环境

在命令行中创建虚拟环境。
创建新的虚拟环境:

```
python3.6 -m venv test 
```

或者

```
pyvenv test 
```

### 激活虚拟环境

创建后，您需要激活虚拟环境。激活虚拟环境

```
cd venv
source bin/activate 
```

然后模块只安装在虚拟环境中，不与其他 python 程序冲突。

学习 Python

*   [https://pythonbasics.org](https://pythonbasics.org)
*   [https://pythonprogramminglanguage.com](https://pythonprogramminglanguage.com)
*   [https://python.org](https://python.org)
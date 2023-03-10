# 随机 Python 教程:使用 virtualenv 启动和运行

> 原文：<https://dev.to/geopopos/random-python-tutorials-up-and-running-with-virtualenv-3lah>

如果您曾经在一台机器上管理过多个 python 项目，那么您可能会遇到这样的问题:不同的项目使用不同版本的特定包。你更新了你正在做的最新项目的包，然后你切换回一个旧的项目，嘣，依赖错误，每个人的最爱。

在本文中，我将简要介绍如何在您的系统上使用 virtualenv。

virtualenv 是一个用来创建和管理多个 python 环境的工具，这样你就不用担心包重叠了。

该工具通过在您指定的位置创建一个全新的 python 安装来工作

## 1。安装 virtualenv

通过 pip 安装 virtualenv:

```
$ pip install virtualenv 
```

检查您的安装

```
$ virtualenv --version 
```

## 2。使用 venv 创建您的第一个虚拟环境

让我们在
中创建一个测试项目/文件夹来创建我们的虚拟环境

```
$ mkdir test_project/
$ cd test_project/
$ virtualenv venv 
```

`virtualenv`命令带有一个参数`DEST_DIR`，它是将在其中创建虚拟环境的目录。我们在`venv`创建了我们的环境，如果你不指定一个目标目录，virtualenv 会把文件放在当前目录

要使用虚拟环境，必须将其激活

```
$ source venv/bin/activate 
```

如果您使用了除`venv`之外的名称，您应该在上面的命令中替换它(例如:`source [folder_name]/bin/activate`)。

您应该在默认 shell 前面看到`(venv)`。

现在我们已经激活了我们的虚拟环境，让我们使用 pip
安装一个包

```
$ pip install requests 
```

现在运行:

```
$ pip freeze 
```

您应该看到`requests`包已经安装好了。

```
certifi==2019.9.11
chardet==3.0.4
idna==2.8
requests==2.22.0
urllib3==1.25.3 
```

现在让我们停用虚拟环境并检查已安装的软件包

```
$ deactivate
$ pip freeze 
```

如果在本教程之前您还没有安装`requests`包，那么您应该不会在已安装的包列表中看到它

这里发生的事情是，当您运行 deactivate 时，您将被放回系统的默认 Python 解释器，而不是虚拟环境的解释器。

虚拟环境中安装了`requests`包。

* * *

感谢您花时间阅读！如果您有任何问题、意见或顾虑，我很乐意倾听！
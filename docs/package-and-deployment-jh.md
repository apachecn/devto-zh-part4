# 第 5 部分打包和部署

> 原文：<https://dev.to/thefern/package-and-deployment-jh>

我们几乎完成了 python 包系列。在本帖中，我们将讨论部署。

*   Github 回购
*   PyInstaller(安装程序)
*   放
*   好吧

## Github

就 github 或任何其他 git 服务器而言，没什么可做的。只要推动你的改变，你就完成了。我建议使用标记，例如用来指示版本，即 v2.0.1。并更新您的自述文件。

## PyInstaller

根据您的包的复杂程度，如果它可以作为 exe 或 dmg 运行，您可以用 [PyInstaller](https://www.pyinstaller.org/) 来打包您的应用程序。

首先安装 pyinstaller:

```
pip install pyinstaller 
```

Enter fullscreen mode Exit fullscreen mode

然后运行:

```
pyinstaller your_file.py 
```

Enter fullscreen mode Exit fullscreen mode

> 这将在名为 dist 的子目录中生成包。

一个文件:

```
pyinstaller your_file.py --onefile 
```

Enter fullscreen mode Exit fullscreen mode

调试:

```
pyinstaller your_file.py --onefile --log-level LEVEL 
```

Enter fullscreen mode Exit fullscreen mode

> 构建时控制台消息的详细程度。级别可以是跟踪、调试、信息、警告、错误、严重(默认:信息)之一。

有关完整用法，请访问[文档](https://pyinstaller.readthedocs.io/en/stable/usage.html)

## 添加到发布中

一旦你有了一个 dmg，或者 exe，你就可以把它添加到 github 的发行版中，这对不知道如何 pip 或者编译代码的最终用户来说很好。这是有据可查的。

## 皮珀

注册一个账户[https://pypi.org/](https://pypi.org/)

[文档](https://packaging.python.org/tutorials/packaging-projects/)

### 打造

> 如果使用 pyinstaller，则删除 dist 文件夹。

让我们首先构建这个包，确保您已经安装了所有的工具。

```
python -m pip install --user --upgrade setuptools wheel 
```

Enter fullscreen mode Exit fullscreen mode

现在从 setup.py 所在的目录运行这个命令:

```
python3 setup.py sdist bdist_wheel 
```

Enter fullscreen mode Exit fullscreen mode

该命令应该输出大量文本，一旦完成，应该在 dist 目录中生成两个文件:

```
dist/
  example_pkg_your_username-0.0.1-py3-none-any.whl
  example_pkg_your_username-0.0.1.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

### 麻线上传到 PyPi

```
python -m pip install --user --upgrade twine 
```

Enter fullscreen mode Exit fullscreen mode

为了便于上传，您可以在主目录中添加一个配置文件:

`.pypirc`

```
[distutils]
index-servers=pypi

[pypi]
repository = https://upload.pypi.org/legacy/
username = your_username
password = your_password 
```

Enter fullscreen mode Exit fullscreen mode

有了`.pypirc`,你可以用下面的命令上传:

```
twine upload dist/* 
```

Enter fullscreen mode Exit fullscreen mode

最后保护`.pypirc`文件，只有当前用户可以读取:

```
chmod 600 ~/.pypirc 
```

Enter fullscreen mode Exit fullscreen mode

> 有几点需要注意:我无法在测试网站上测试 pypi。所以我最后只在真实的网站上做了。确保 setup.py 中的版本号和所有信息都是正确的。上传版本后，除非更改版本号，否则不能再次重新上传。

## 安装您的软件包

几秒钟后，检查您的 PyPi 帐户中的包，如果有的话。通过 pip 在本地机器上安装它。你终于完成了第一次部署。

## 重述

每个版本:

*   Update git repo
*   上传 git 版本(可选)
*   使用安装工具编译
*   通过 twine 上传到 PyPi

恭喜你坚持到了最后！欢迎任何反馈！记住不要在星期五部署。:)
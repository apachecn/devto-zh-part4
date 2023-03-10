# 使用 Pyenv 和 Pipenv 的 Python 虚拟环境

> 原文：<https://dev.to/spacerockmedia/the-python-virtual-environment-with-pyenv-pipenv-3mlo>

最初发表在我的网站上，地址为 [writingco.de](https://writingco.de) 。这是我的内容博客的交叉帖子。我每周都会发布新的内容，如果你想直接在收件箱里收到我的文章和其他信息，你可以注册我的[时事通讯](https://writingco.de/newsletter)！

围绕打包和管理 python 虚拟环境有一段混乱的历史。我们用 pyvenv 吗？venv？virtualenvwrapper？最近，其他选择突然出现。使用 [pyenv](https://github.com/pyenv/pyenv) 给了我们管理 python 版本的能力，就像`nvm`和`rvm`一样，而 [Pipenv](https://github.com/pypa/pipenv) 是 pip 本身的继承者，最终将被合并。

## 安装 Pyenv

Pyenv 允许您轻松地在系统上同时安装不同版本的 python，而不会产生冲突。它在 windows 上不受支持，但是 python 可以很容易地从他们的网站上安装。<mark id="annotation-text-3b71a6a0-d22b-4ac0-8787-25b139fb0829">确保将它添加到您的窗口`PATH`。</mark>

安装非常简单，只需运行这些命令就可以完成安装。

```
$ git clone https://github.com/pyenv/pyenv.git ~/.pyenv
$ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bash_profile
$ echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile 
```

Enter fullscreen mode Exit fullscreen mode

如果你有任何问题，在他们的回购页面上有更多的安装细节。他们也有对`zsh`用户的说明。您可能需要在安装后重新加载您的终端，以使 pyenv 命令可用。

```
➜ pyenv
pyenv 1.2.12-2-geb68ec94
Usage: pyenv <command> [<args>]

Some useful pyenv commands are:
   commands List all available pyenv commands
   local Set or show the local application-specific Python version
   global Set or show the global Python version
   shell Set or show the shell-specific Python version
   install Install a Python version using python-build
   uninstall Uninstall a specific Python version
   rehash Rehash pyenv shims (run this after installing executables)
   version Show the current Python version and its origin
   versions List all Python versions available to pyenv
   which Display the full path to an executable
   whence List all Python versions that contain the given executable

See `pyenv help <command>' for information on a specific command.
For full documentation, see: https://github.com/pyenv/pyenv#readme 
```

Enter fullscreen mode Exit fullscreen mode

我们今天要处理的主要子命令是`local`、`global`和`install`。首先，让我们看看我们的系统上有哪些版本。

```
➜ pyenv versions
* system 
```

Enter fullscreen mode Exit fullscreen mode

### 安装新版本

对于大多数用户来说，你只有系统解释器。让我们安装 3.7 作为 python <mark id="annotation-text-206ad3df-a7b5-4462-857b-35d31161f3e3">的另一个全球版本。</mark> 

```
➜ pyenv install 3.7
python-build: definition not found: 3.7

The following versions contain `3.7' in the name:
  2.3.7
  3.3.7
  3.7.0
  3.7-dev
  3.7.1
  3.7.2
  3.7.3
  miniconda-3.7.0
  miniconda3-3.7.0
  stackless-3.3.7

See all available versions with `pyenv install --list'.
... 
```

Enter fullscreen mode Exit fullscreen mode

如果我们要求的版本只是部分匹配，它会告诉我们可以安装哪些版本。让我们安装最新的，这将需要几分钟。

```
➜ pyenv install 3.7.3
Downloading Python-3.7.3.tar.xz...
-> https://www.python.org/ftp/python/3.7.3/Python-3.7.3.tar.xz
Installing Python-3.7.3...
Installed Python-3.7.3 to /home/shawn/.pyenv/versions/3.7.3 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们让 3.7.3 全球可用，这样就不会干扰我们的系统 python:

```
➜ pyenv global      
system
➜ pyenv global 3.7.3
➜ pyenv global      
3.7.3
➜ pyenv versions
  system
* 3.7.3 (set by /home/shawn/.pyenv/version) 
```

Enter fullscreen mode Exit fullscreen mode

我们现在默认为 3.7.3。目前，我们已经完成了 pyenv。这是一个非常简单的工具。

## Pipenv:Python 虚拟环境

要安装 pipenv，您可以通过您系统的软件包管理器进行安装，如这里的所述。

### 设置

开始之前，我们需要创建一个简单的项目。让我们克隆一个我拥有的 [repo](https://gitlab.com/autoferrit/template) ,其中有几个用于在 git 中跟踪项目的基本文件。

```
➜ git clone git@gitlab.com:autoferrit/template.git demo
Cloning into 'demo'...
remote: Enumerating objects: 6, done.
remote: Counting objects: 100% (6/6), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 6 (delta 0), reused 0 (delta 0)
Receiving objects: 100% (6/6), done.
➜ cd demo
➜ ls -la
Permissions Size User Date Modified Git Name
drwxr-xr-x - shawn 27 Jun 12:10 -- .git
.rwxr-xr-x 474 shawn 27 Jun 12:09 -- .editorconfig
.rwxr-xr-x 2.2k shawn 27 Jun 12:09 -- .gitattributes
.rw-r--r-- 138 shawn 27 Jun 12:09 -- Pipfile
.rw-r--r-- 96 shawn 27 Jun 12:09 -- Readme.md 
```

Enter fullscreen mode Exit fullscreen mode

您可以在 GitLab 项目中看到文件内容。

*   帮助告诉编辑(用适当的插件)在这个项目中的文件上应该使用什么格式选项。
*   在各种平台上工作时(我在看着你，windows)，这样可以控制行尾。
*   这是我们保留应用程序需求的地方，而不是使用 requirements.txt

Pipfile 也将由下面的命令生成。

```
➜ pipenv --python 3.7.3 install 
```

Enter fullscreen mode Exit fullscreen mode

这将创建您的 python 虚拟环境，然后您可以通过运行`pipenv shell`来使用它。最好的部分是 Pipenv 支持 Pyenv，并将使用 python 版本安装，使用该工具使我们的系统更加干净。

## 一个简单的 API

为了展示如何使用它，我们将使用 FastAPI 创建一个非常基本的 API。用下面的命令安装需求:

```
➜ pipenv install fastapi uvicorn 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个类似于 node.js 中的`package-lock.json`的`Pipfile.lock`，当代码通过 CI 服务器或产品运行时，创建一个可重复的环境是至关重要的，这也是 Pipenv 的优势所在。它确保我们可以实际部署相同版本的包及其需求。让我们运行这段代码。

```
➜ uvicorn main:app --reload 
```

Enter fullscreen mode Exit fullscreen mode

现在访问[http://127 . 0 . 0 . 1:8000/items/5 就可以看到你的 API 了？q=somequery](http://127.0.0.1:8000/items/5?q=somequery) 。如果你想继续学习本教程，你可以在他们的文档[中阅读 fastapi 教程。](https://fastapi.tiangolo.com/#example)

### Pipfile

让我们打开 Pipfile，看看 python 虚拟环境中安装了什么。

```
[[source]]
name = "pypi"
url = "https://pypi.org/simple"
verify_ssl = true

[dev-packages]

[packages]
fastapi = "*"
uvicorn = "*"

[requires]
python_version = "3.7" 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，Pipenv 将总是安装最新版本的软件包。这是我们都应该做的。当新版本发布时，我们运行`pipenv install`，它将安装已发布的新版本。要更新一个包，我们可以运行`pipenv update fastapi`，或者如果我们想更新所有的包，我们可以运行`pipenv update`。如果我们需要固定一个特定的版本，如果你使用标准的`pip`和`requirements.txt`，你也可以用同样的方法。

### 如何在我的环境中安装开发包？

要安装开发包，只需传递`--dev`参数。

```
➜ pipenv install --dev ptpython 
```

Enter fullscreen mode Exit fullscreen mode

并安装开发包:

```
➜ pipenv install --dev 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，`pipenv install`将只安装应该存在于产品中的基础包。这就是我们想要的。传递`--dev`参数将允许我们在本地或在 CI 过程中安装基础和开发包。

### 美元外壳中的 pip

要激活环境，只需运行

```
demo on writingcode master [!?] 
➜ pipenv shell        
Launching subshell in virtual environment…
 . /home/shawn/code/sandbox/demo/.venv/bin/activate

demo on writingcode master [!?] via demo 
➜ pip list    
Package Version
--------------- -------
Click 7.0    
fastapi 0.30.0 
h11 0.8.1  
httptools 0.0.13 
pip 19.1.1 
pydantic 0.28   
setuptools 41.0.1 
starlette 0.12.0 
uvicorn 0.8.2  
uvloop 0.12.2 
websockets 7.0    
wheel 0.33.4 
```

Enter fullscreen mode Exit fullscreen mode

注意:我在这里展示了完整的终端文本，以表明它现在正在 python 环境的提示符下添加`via demo`。你可能会看到这是不同的表现。通常看起来是这样的:

```
(demo) $ pip list
... 
```

Enter fullscreen mode Exit fullscreen mode

## 检查虚拟环境中的漏洞

最好的特性之一可能是检查漏洞的能力。

```
➜ pipenv check
Checking PEP 508 requirements…
Passed!
Checking installed package safety…
All good! 
```

Enter fullscreen mode Exit fullscreen mode

我们的 python 虚拟环境中没有漏洞。这是件好事。我们试着加一个。

```
➜ pipenv install 'django==2.1'
➜ pipenv check
Checking PEP 508 requirements…
Passed!
Checking installed package safety…
36883: django <2.1.6,>=2.1.0 resolved (2.1 installed)!
Django 2.1.x before 2.1.6 allows Uncontrolled Memory Consumption via a malicious attacker-supplied value to the django.utils.numberformat.format() function.

36522: django <2.1.2,>=2.1 resolved (2.1 installed)!
An issue was discovered in Django 2.1 before 2.1.2, in which unprivileged users can read the password hashes of arbitrary accounts. The read-only password widget used by the Django Admin to display an obfuscated password hash was bypassed if a user has only the "view" permission (new in Django 2.1), resulting in display of the entire password hash to those users. This may result in a vulnerability for sites with legacy user accounts using insecure hashes.

36517: django <2.1.2,>=2.1.0 resolved (2.1 installed)!
django before 2.1.2 fixes a security bug in 2.1.x. 
If an admin user has the change permission to the user model, only part of the
password hash is displayed in the change form. Admin users with the view (but
not change) permission to the user model were displayed the entire hash. 
```

Enter fullscreen mode Exit fullscreen mode

哇哦！不要安装 Django 2.1。幸运的是它被修复了，让我们更新它。

```
➜ pipenv update django
Locking [dev-packages] dependencies…
Locking [packages] dependencies…
✔ Success! 
Updated Pipfile.lock (9d4a23)!
Installing dependencies from Pipfile.lock (9d4a23)…
  🐍 ▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉ 11/11 — 00:00:01
All dependencies are now up-to-date!

➜ pip list            
Package Version
--------------- -------
...  
Django 2.1    
... 
```

Enter fullscreen mode Exit fullscreen mode

等等为什么装的是老版本？这很好！为什么？这意味着我们的设置是幂等的。为什么？因为我们钉住了 Django 的“2.1”版本，所以没用。所以我们应该通过将版本更改为`"*"`来在 Pipfile 中取消固定。我们现在应该能够得到预期的结果。

```
➜ pipenv install            
Pipfile.lock (10cb8d) out of date, updating to (9d4a23)…
Locking [dev-packages] dependencies…
Locking [packages] dependencies…
✔ Success! 
Updated Pipfile.lock (10cb8d)!
Installing dependencies from Pipfile.lock (10cb8d)…
  🐍 ▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉ 12/12 — 00:00:04

➜ pip list      
Package Version
--------------- -------
...  
Django 2.2.2  
... 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

Pyenv 和 Pipenv 使得检查 python 虚拟环境和版本变得更加容易。现在，您可以让您的环境跨系统一致地部署。当谈到跨系统一致地安装需求时，Python 有一个令人头疼的历史。使用这些工具将有助于您更加专注于您的实际代码。

不幸的是，Pipenv 并不适合那些需要 setup.py 才能将库上传到 pypi 的人

这是订阅我的时事通讯的链接。你也可以在推特上关注我。
T3![](img/ad9b9da1674b387c7b673d8f5867bfc0.png)T5】

在 Python 中，你如何管理你的环境和依赖关系？
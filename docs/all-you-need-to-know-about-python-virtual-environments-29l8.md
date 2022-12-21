# 关于 python 虚拟环境，您需要知道的一切

> 原文：<https://dev.to/gajesh/all-you-need-to-know-about-python-virtual-environments-29l8>

几乎每个参与过任何项目并使用过 python 包的人都不得不处理不同分支和项目中不同版本的包。虚拟环境使这一过程变得更加容易。

## 为什么我不应该直接在我的系统上安装 python 包？

好吧，你可以，但你必须在未来处理它。随着今天越来越多的程序和项目用 python 编写，你不能期望它们都使用相同版本的相同包。有时，项目的测试分支可能使用库的更新版本，而生产可能使用旧的测试版本。如果你决定在你的系统上使用 python 包，而没有人希望这样，那么你的基于 python 的系统依赖(当然在 Ubuntu 的情况下)可能会崩溃。不管怎样，最好详细了解虚拟环境。

## 安装和使用

在本教程中，我们将使用`virtualenv`来管理一个单独的虚拟环境。

1.  首先创建一个简单的测试目录或项目目录。使用命令行进入该目录。

2.  在你使用 Python 3 的情况下，使用命令`pip install virtualenv`或`pip3 install virtualenv`在你的系统中安装`virtualenv`。

**注意** : Python 在标准库 3.3 以后的版本中[包含了](https://docs.python.org/3/library/venv.html) venv 模块。

1.  键入命令`virtualenv venv`创建一个名为 venv 的虚拟环境，这将创建一个名为 venv 的文件夹，其中包含我们需要的所有 python 可执行文件，以便在一个独立的项目环境中开始工作，而不会干扰现有的系统设置和其他项目的依赖性。这将为您提供标准的工作环境，并使用您的默认系统 python 解释器作为默认环境的语言。

2.  **示例**:语法是`virtualenv name_of_virtual_env_folder`，其中`name_of_virtual_env_folder`可以有任何不带空格的名字。您也可以通过键入`virtualenv`来省略名称，但是文件将在您当前的项目目录中创建。

3.  您还可以通过运行命令`virtualenv -p /usr/bin/python3.6 venv`来更改您正在使用的 python 解释器。这将在您的虚拟环境中提供 python 3.6(用您喜欢的任何有效版本替换 3.6，安装在您的系统上)。

4.  如果不希望将`venv`写入源代码控制，可以将`venv`添加到`.gitignore`中。(git 会忽略)

要启动虚拟环境，您可以键入。`source venv/bin/activate` **请记住每次你想在项目中工作时运行它，或者你可以把它添加到你的设置**脚本中。当虚拟环境被激活时，你的 shell 提示符将看起来类似于`(name_of_virtual_environment) yourusername@yourcomputername:~/project_directory$`。

与往常一样，在 Windows 上激活虚拟环境的方式略有不同。你可以按照《搭车人 Python 指南》中提到的`C:\Users\SomeUser\project_folder> venv\Scripts\activate`那样做。

从现在开始，您使用 pip 安装的任何包都将被放置在`venv`文件夹中，与上的全局 Python 安装相隔离。您可以通过将虚拟环境中当前拥有的所有包“冻结”在一个名为`requirements.txt`的文件中来保持环境的一致性。这使得其他开发人员能够跨项目和项目中的分支保持一致性。

要这样做，您可以稍后在新的环境中使用这个来再次安装相同的包，如果需要的话，通过键入`pip install -r requirements.txt`。

当完成项目时，不要忘记通过键入`source deactivate`来取消激活，你将回到你的系统默认解释器。您也可以通过删除文件夹来删除虚拟环境。

## 虚拟包装

`virtualenvwrapper`提供了一组命令，使虚拟环境的工作变得更加容易，并将所有的虚拟环境放在一个地方。您可以在文档中查看安装说明,并按照您的操作系统和 shell 的步骤进行操作。(巴什、ZSH 等。).

### 使用 virtualenvwrapper 的一些基本例子

您可以使用以下命令安装和设置包装器。

```
pip install virtualenvwrapper
export WORKON_HOME=~/Envs
source /usr/local/bin/virtualenvwrapper.sh 
```

Enter fullscreen mode Exit fullscreen mode

当您执行`export WORKON_HOME=~/Envs`命令时，它会将所有使用`virtualenv`包装器创建的未来虚拟环境放在`~/Envs`文件夹中。

### 创建和使用项目

如文档中所示，使用包装器创建和维护项目是非常容易的。

1.  创建虚拟环境:`$ mkvirtualenv project_folder`

这将在`~/Envs`中创建`project_folder`文件夹。

1.  在虚拟环境中工作:`$ workon project_folder`

或者，您可以创建一个项目，这将创建虚拟环境，还可以在$WORKON_HOME 中创建一个项目目录，当您在 workonproject_folder 中工作时，会将该目录刻录到 cd 中。
`$ mkproject project_folder`

**virtualenvwrapper** 提供环境名称的制表符补全。当你有很多环境，并且很难记住它们的名字时，这很有帮助。

此外，还可以关闭您当前所处的环境，这样您就可以在不同的环境之间快速切换。

1.  停用还是一样:
    `$ deactivate`

2.  删除:
    `$ rmvirtualenv venv`

有关更多命令和细节，您可以查看 [virtualenvwrapper 文档](https://virtualenvwrapper.readthedocs.io/en/latest/command_ref.html)。

## 高级用法和 Pipenv

`Virtualenv`为您提供具有基本环境隔离的低级可视化功能。然而，它缺乏成熟的软件包管理器的其他特性，这就是 **Pipenv** 的用武之地。它有许多特性，可以为大规模的复杂项目创建和管理包。我将简单地介绍一下，因为`pipenv`的文档很容易阅读并且写得很好。

### 安装及基本用法

`Pipenv`将所有的包信息存储在一个名为`Pipfile`和`Pipfile.lock`的文件中，该文件包含了包、版本和源代码(Pypi 或定制 Github 源代码)的所有细节。

下面给出了`Pipfile`和`Pipfile.lock`的简单示例(文档的编辑版本)

**Pipfile** :

```
[[source]]
url = "https://pypi.python.org/simple"
verify_ssl = true
name = "pypi"
​
[packages]
numpy = "*"
​
[dev-packages]
pytest = "*" 
```

Enter fullscreen mode Exit fullscreen mode

**Pipfile.lock** :(我的`Pipfile.lock`创建一个全新的 Pipenv 环境并安装`numpy` )

```
{
    "_meta": {
        "hash": {
            "sha256": "532f630f67db39ae5920f79895733204f9869909ded64df233d99611b657a39c"
        },
        "pipfile-spec": 6,
        "requires": {
            "python_version": "2.7"
        },
        "sources": [
            {
                "name": "pypi",
                "url": "https://pypi.org/simple",
                "verify_ssl": true
            }
        ]
    },
    "default": {
        "numpy": {
            "hashes": [
                "sha256:1980f8d84548d74921685f68096911585fee393975f53797614b34d4f409b6da",
                "sha256:22752cd809272671b273bb86df0f505f505a12368a3a5fc0aa811c7ece4dfd5c",
                "sha256:23cc40313036cffd5d1873ef3ce2e949bdee0646c5d6f375bf7ee4f368db2511",
                ....
            ],
            "index": "pypi",
            "version": "==1.16.2"
        }
    },
    "develop": {}
} 
```

Enter fullscreen mode Exit fullscreen mode

我将在一分钟内解释这些东西的意思以及如何开始使用`Pipenv`首先，让我们继续安装软件包

键入`pip install pipenv`安装软件包。

创建项目文件夹或导航到项目文件夹。

```
$ mkdir env_experiments
$ cd env_experiments 
```

Enter fullscreen mode Exit fullscreen mode

您现在可以使用`pipenv install`来设置一个使用`pipfile`的现有环境，或者它为您创建一个新的`pipfile`。如果运行`pipenv install`时同一文件夹中只有一个`requirements.txt`文件，pipenv 会自动导入`requirements.txt`文件的内容。

如果你想为你现有的项目安装一个包，你可以输入不带< >括号的`pipenv install <package_name>`。这将创建一个不存在的`Pipfile`。如果它确实存在，它会自动用您提供的新包进行编辑。

现在可以输入`pipenv shell`和 vol-la 了！你在你的虚拟环境中。

你可以输入`python --version`并找出你的虚拟环境运行的是哪个版本的 python。

### 安装和运行软件包

您可以通过在虚拟环境中运行`pipenv install <package_name>`来安装任何软件包，对于特定版本的软件包安装，请使用`~=`。

`$ pipenv install requests~=1.2 # equivalent to requests~=1.2.0`

这将自动更新您的`Pipfile`以反映这一要求。

您还可以使用<= =>进行版本比较。下面给出了一个例子

```
$ pipenv install "requests>=1.4"   # will install a version equal or larger than 1.4.0
$ pipenv install "requests<=2.13"  # will install a version equal or lower than 2.13.0
$ pipenv install "requests>2.19"   # will install 2.19.1 but not 2.19.0 
```

Enter fullscreen mode Exit fullscreen mode

### 升级软件包

这简单明了。可以跑`pipenv update`。为了更新项目中的包或特定包的更新，运行`pipenv update <package_name>`你也可以使用命令`pipenv update --outdated`检查是否有更新，并删除那个包。

### 指定 Python 版本

您可以通过运行`$ pipenv --python version_number`来指定虚拟环境将使用的 python 版本

**例子** :

```
$ pipenv --python 3 # Creates Python3 environment
$ pipenv --python 3.6 # Createst Python 3.6 environments 
```

Enter fullscreen mode Exit fullscreen mode

当给定一个 Python 版本时，Pipenv 会自动扫描您的系统，寻找与给定版本匹配的 Python。

如果还没有创建一个`Pipfile`，将会为你创建一个，否则，python 版本信息将会附加在现有`Pipfile`的末尾，如下所示:

```
[[source]]
url = "https://pypi.python.org/simple"
verify_ssl = true
​
[dev-packages]
​
[packages]
​
[requires]
python_version = "3.6" 
```

Enter fullscreen mode Exit fullscreen mode

如果您没有在命令行上指定 python 版本，那么在执行时，它会回到您系统的默认 Python 安装。

### 管理虚拟环境

#### $ pipenv 安装

`$ pipenv install`用于将软件包安装到 pipenv 虚拟环境中，并更新您的 Pipfile。

以及基本的安装命令，其形式为:

`$ pipenv install [package names]`

#### $ pipenv 卸载

`$ pipenv uninstall`支持`pipenv install`中的所有[参数](https://pipenv.readthedocs.io/en/latest/basics/#pipenv-install)，以及另外两个选项`--all`和`--all-dev`。

#### $ pipenv 锁

`$ pipenv lock`用于创建一个`Pipfile.lock`，它向**声明项目的所有**依赖项(和子依赖项),它们的最新可用版本，以及下载文件的当前散列。这确保了可重复的，最重要的是*确定性*的构建。

这足以让您使用虚拟环境和 Python。肯尼斯·☤·雷茨创建了如此漂亮的库，并为其编写了出色的文档，我从文档中选取了大部分代码片段作为示例和一些解释。

你可以在 dev.to 或 [Medium](https://medium.com/@gajeshbhat) 或 [Twitter](https://twitter.com/gajeshbhat) 上阅读更多我的文章并关注我。

欢迎评论和建议。

## 参考文献及进一步阅读

1.  [Python 虚拟环境指南](https://docs.python-guide.org/dev/virtualenvs/)
2.  [Pipenv 文档:基础知识](https://pipenv.readthedocs.io/en/latest/basics/)
3.  [Pipenv 文档:高级](https://pipenv.readthedocs.io/en/latest/advanced/)
4.  [关于 Pipenv 的常见问题解答](https://pipenv.readthedocs.io/en/latest/diagnose/)
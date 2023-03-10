# 如何建立一个完美的 Python 项目

> 原文：<https://dev.to/sourcery/how-to-set-up-a-perfect-python-project-4871>

当开始一个新的 Python 项目时，很容易一头扎进去开始编码。花很少的时间用最好的工具设置一个项目将会节省大量的时间，并带来更愉快的编码体验。

在理想的世界中，所有开发人员的依赖关系都是一样的，代码
格式完美，常见错误被禁止，所有事情都被测试覆盖。此外，所有这些都将在每次提交时得到保证。

在本文中，我将介绍如何建立一个项目来实现这一点。您可以按照步骤操作，或者直接通过安装 [pipx](#pipx) 和 [pipenv](#pipenv) 然后[生成新项目](#generate)来自动生成新项目。

让我们创建一个新的项目目录:

```
mkdir best_practices
cd best_practices 
```

# Python 命令行工具与 pipx

Pipx 是一个方便的工具，允许快速安装 python 命令行工具。我们将用它来安装 pipenv 和 cookiecutter。

```
python3 -m pip install --user pipx
python3 -m pipx ensurepath 
```

# 用 pipenv 进行依赖管理

> [Pipenv](https://github.com/pypa/pipenv) 自动为您的项目创建和管理一个
> virtualenv，并在您安装/卸载软件包时从您的
> Pipfile 中添加/删除软件包。它还生成了非常重要的
> Pipfile.lock，用于生成确定性的构建。

知道你和你的队友使用相同的库版本是一个巨大的信心提升。Pipenv 解决了这个问题，并在去年左右获得了巨大的吸引力。

```
pipx install pipenv 
```

# 用黑色和 isort 编码格式化

[黑色](https://github.com/psf/black)格式化我们的代码:

> Black 是不折不扣的 Python 代码格式化程序。通过使用它，您同意放弃对手写格式的细节的控制。作为回报，黑色给了你速度、确定性和摆脱 pycodestyle 对格式的困扰。你将节省时间和精力去做更重要的事情。
> 
> 不管你在读什么项目，黑化的代码看起来都一样。一段时间后，格式变得透明，您可以专注于内容。
> 
> Black 通过产生尽可能小的差异来加快代码审查。

当 [isort](https://github.com/timothycrosley/isort) 分拣我们的进口商品时:

> isort python 会为您导入，因此您不必这么做。isort 是一个 Python 工具/库，可以按字母顺序对导入进行排序，并自动分成几个部分。

让我们使用 pipenv 作为开发依赖项来安装它们，这样它们就不会扰乱部署:

```
pipenv install black isort --dev 
```

Black 和 isort 有不兼容的默认选项，所以我们将覆盖 isort 以跟随 black 的领导。创建一个`setup.cfg`文件并添加这个配置:

```
[isort]
multi_line_output=3
include_trailing_comma=True
force_grid_wrap=0
use_parentheses=True
line_length=88 
```

我们可以使用
运行这些工具

```
pipenv run black
pipenv run isort 
```

# 用 flake8 执行样式

Flake8 确保我们的代码遵循 PEP8 中定义的标准 python 约定。使用 pipenv:
安装

```
pipenv install flake8 --dev 
```

就像 isort 一样，它需要一点配置才能很好地与黑色搭配。将此配置添加到`setup.cfg` :

```
[flake8]
ignore = E203, E266, E501, W503
max-line-length = 88
max-complexity = 18
select = B,C,E,F,W,T4 
```

现在我们可以用`pipenv run flake8`运行 flake8 了。

# 静态类型有 mypy

> Mypy 是 Python 的一个可选静态类型检查器，旨在结合动态(或“duck”)类型和静态类型的优点。Mypy 将 Python 的表达能力和便利性与强大的类型系统和编译时类型检查相结合。Mypy 类型检查标准 Python 程序；使用任何 Python 虚拟机运行它们，基本上没有运行时开销。

在 Python 中使用类型需要一点时间来适应，但是好处是巨大的。从网站:

*   静态类型可以使程序更容易理解和维护
*   静态类型可以帮助您更早地发现错误，减少测试和调试
*   静态类型可以帮助您在代码投入生产之前发现难以发现的错误

```
pipenv install mypy --dev 
```

默认情况下，Mypy 将递归检查所有类型注释的导入，当库不包含这些注释时，这会导致错误。
我们需要将 mypy 配置为只在我们的代码上运行，并忽略任何没有类型注释的导入错误。
我们假设我们的代码存在于下面配置的`best_practices`包中。将此添加到`setup.cfg` :

```
[mypy]
files=best_practices,test
ignore_missing_imports=true 
```

现在我们可以用:
运行 mypy

```
pipenv run mypy 
```

这里有一个有用的[备忘单](https://mypy.readthedocs.io/en/latest/cheat_sheet_py3.html)来使用它。

# 用 pytest 和 pytest-cov 进行测试

使用 [pytest](https://docs.pytest.org/en/latest/) 编写测试非常容易，消除编写测试的任何障碍意味着我们将编写更多的测试！

```
pipenv install pytest pytest-cov --dev 
```

这里有一个来自 pytest 网站的简单例子:

```
# content of test_sample.py def inc(x):
    return x + 1

def test_answer():
    assert inc(3) == 5 
```

要执行它:

```
$ pipenv run pytest
=========================== test session starts ============================
platform linux -- Python 3.x.y, pytest-5.x.y, py-1.x.y, pluggy-0.x.y
cachedir: $PYTHON_PREFIX/.pytest_cache
rootdir: $REGENDOC_TMPDIR
collected 1 item

test_sample.py F                                                     [100%]

================================= FAILURES =================================
_______________________________ test_answer ________________________________

    def test_answer():
>       assert inc(3) == 5
E       assert 4 == 5
E        +  where 4 = inc(3)

test_sample.py:6: AssertionError
========================= 1 failed in 0.12 seconds ========================= 
```

我们所有的测试都应该放在`test`目录中，所以将这个配置添加到`setup.cfg` :

```
[tool:pytest]
testpaths=test 
```

我们还想检查测试覆盖了多少代码。创建一个新文件`.coveragerc`，只返回我们的应用程序代码的覆盖率统计数据，我们再次假设我们的应用程序代码位于`best_practices`模块:

```
[run]
source = best_practices

[report]
exclude_lines =
    # Have to re-enable the standard pragma
    pragma: no cover

    # Don't complain about missing debug-only code:
    def __repr__
    if self\.debug

    # Don't complain if tests don't hit defensive assertion code:
    raise AssertionError
    raise NotImplementedError

    # Don't complain if non-runnable code isn't run:
    if 0:
    if __name__ == .__main__.: 
```

我们现在可以用
运行我们的测试并报告覆盖率

```
pipenv run pytest --cov --cov-fail-under=100 
```

如果我们对应用程序代码的测试覆盖率低于 100%，这将会失败。

# 带预提交的 Git 挂钩

Git 钩子允许你在任何你想提交或推送的时候运行脚本。这让我们可以在每次提交/推送时自动运行所有的林挺和测试。[预提交](https://pre-commit.com/)允许轻松配置这些挂钩:

> Git hook 脚本对于在提交给代码评审之前识别简单的问题非常有用。我们在每次提交时运行钩子，自动指出代码中的问题，比如缺少分号、尾随空格和调试语句。通过在代码评审之前指出这些问题，这允许代码评审者将注意力集中在变更的架构上，而不是在琐碎的风格吹毛求疵上浪费时间。

在这里，我们将上述所有工具配置为在提交时在任何更改的 python 文件上运行，并且仅在推送时运行 pytest 覆盖率，因为推送可能会很慢。创建一个新文件`.pre-commit-config.yaml` :

```
repos:
  - repo: local
    hooks:
      - id: isort
        name: isort
        stages: [commit]
        language: system
        entry: pipenv run isort
        types: [python]

      - id: black
        name: black
        stages: [commit]
        language: system
        entry: pipenv run black
        types: [python]

      - id: flake8
        name: flake8
        stages: [commit]
        language: system
        entry: pipenv run flake8
        types: [python]
        exclude: setup.py

      - id: mypy
        name: mypy
        stages: [commit]
        language: system
        entry: pipenv run mypy
        types: [python]
        pass_filenames: false

      - id: pytest
        name: pytest
        stages: [commit]
        language: system
        entry: pipenv run pytest
        types: [python]

      - id: pytest-cov
        name: pytest
        stages: [push]
        language: system
        entry: pipenv run pytest --cov --cov-fail-under=100
        types: [python]
        pass_filenames: false 
```

如果你需要跳过这些钩子，你可以运行`git commit --no-verify`或者`git push --no-verify`

# 使用 cookiecutter 生成项目

现在我们已经看到了一个理想的项目包含什么，我们可以把它变成一个[模板](https://github.com/sourcery-ai/python-best-practices-cookiecutter)，用一个命令
生成一个新的项目

```
pipx run cookiecutter gh:sourcery-ai/python-best-practices-cookiecutter 
```

填写项目名称和回购名称，您的项目将为您生成。

要完成设置，请遵循以下步骤:

```
# Enter project directory
cd <repo_name>

# Initialise git repo
git init

# Install dependencies
pipenv install --dev

# Setup pre-commit and pre-push hooks
pipenv run pre-commit install -t pre-commit
pipenv run pre-commit install -t pre-push 
```

模板项目包含一个非常简单的 Python 文件和测试，用于测试这些工具。一旦你对代码满意了，你就可以开始你的第一个`git commit`，所有的钩子都将运行。

# 编辑整合

虽然知道我们项目的代码标准在提交代码时将始终保持在最高水平是很好的，但在我们认为代码更改已经全部完成后发现任何问题有点令人沮丧。实时显示问题要好得多。

在保存文件时，花些时间确保这些命令是由代码编辑器运行的。拥有即时反馈意味着您可以在代码记忆犹新的时候快速修复任何引入的小问题。

就我个人而言，我使用一些优秀的 Vim 插件来实现这一点:

*   [ale](https://github.com/dense-analysis/ale) 实时运行 flake8，在文件保存时运行 black、isort 和 mypy
*   [vim-test](https://github.com/janko/vim-test) 与[放映员集成](https://github.com/janko/vim-test#projectionist-integration)在文件保存时运行 pytest
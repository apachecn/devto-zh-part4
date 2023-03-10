# Python 代码质量的 5 大利器

> 原文：<https://dev.to/madelyneriksen/5-awesome-tools-for-python-code-quality-2pc>

*备选标题:一名开发人员在她的`.travis.yml`文件中添加了五行。你不会相信接下来会发生什么！*

代码质量对于保持大型项目健康发展至关重要，但是手工维护一致的质量很难。然而，有许多 Python 代码工具可以让这变得容易得多。

像软件开发中的其他几个主题一样，使用什么工具取决于个人偏好。您是否使用了不在此列表中的工具？随时让我知道我错过了什么！以下是截至 2019 年我个人对工具的偏好。

## 1。Pytest -基本单元测试

Python 在`unittest`模块中有一个内置的单元测试工具包，但是它并不总是适合每个项目。我更喜欢尽可能使用 [Pytest](https://pytest.org) ，因为这种体验非常简单。

**为什么好**

当编写单元测试变得更容易时，你会编写更多的单元测试。Pytest 使得创建新的测试变得轻而易举。只需添加一个以`test`开头的新 Python 文件，编写几个函数，就大功告成了。

Pytest 有大量插件可以通过一个`pytest.ini`文件进行配置。编写自己的插件也很容易，没有痛苦！当然，Pytest 也“只适用于”来自`unittest`的基于类的测试。

**如何使用**

假设你正在写一个测试，看看加法是否有效，在这个交替的宇宙中，它也没有打扰你，这个练习看起来是如此的做作。您可以在`tests`目录中编写一个类似这样的 pytest 文件:

```
"""An Example Pytest Test"""

import time
import pytest

def test_plus_one():
    """Test some addition."""
    x = 8 + 1
    assert x == 9

@pytest.mark.integration
def slow_test():
    """A really slow integration test."""
    time.sleep(5)
    assert 1 == 1 
```

你也可以写一个`pytest.ini`文件来注册“慢”标记，否则你会得到一个令人不快的警告:

```
[pytest]
markers =
    integration: Integration test. 
```

和...就是这样。这就是创建新的 Pytest 测试所需的全部内容。您可以通过从 PyPi 安装 Pytest 并运行`pytest`命令来运行它们。

```
pip install pytest
pytest 
```

就这么简单。🎉

## 2。Pylint 风格的检查和错误定位

[Pylint](https://www.pylint.org/) 是一个*智能*代码分析工具，可以理解你的 Python 代码。它捕捉常见的错误，如拼写错误的变量名，以及不返回分支等隐蔽的问题。

**为什么好**

与许多其他用于林挺 Python 代码的工具不同，Pylint 可以发现一些会导致异常的逻辑错误或代码。它还实施了许多最佳实践，比如让`try ... except`块专注于几个*特定的*异常类，并避免不良的默认参数，比如空列表或字典。

Pylint 在严格性上是无与伦比的——它有很多检查，从多余的警告到严重的错误。使用 Pylint 的缺点是有时检查是错误的；当你知道自己是对的时，你会想把你的`.pylintrc`文件合上。

**如何使用**

像大多数 Python 工具一样，您可以使用`pip` :
从 PyPi 下载 Pylint

```
pip install pylint
# Lint your project with the module (folder) name
pylint my_project 
```

您可能还想为您的项目创建一个`.pylintrc`文件。这个文件也应该被签入到源代码控制中。Pylint 也可以为您生成一个默认的`.pylintrc`文件:

```
pylint --generate-rcfile > .pylintrc 
```

如果你不喜欢使用像 [GNU Make](https://www.madelyneriksen.com/blog/gnu-make-for-beginners) 这样的任务运行器或构建器，也有将 Pylint 与 Pytest 集成的插件。

## 3。Python 的黑码格式

当你可以将工作外包给计算机时，为什么要花费精力美化你的代码呢？ [Black](https://github.com/python/black) 是 Python 的一个代码格式化程序，它是快速的和 100%静态的——它不导入代码来格式化它。

**为什么好**

就我个人而言，虽然我喜欢干净漂亮的 Python 代码，但是手工格式化代码是令人疲惫不堪的。将行放入源文件要容易得多，不用担心换行、缩进或长函数签名。

Black 从程序员那里拿走了所有这些责任，取而代之的是格式化代码本身。这是快速和有效的默认减少“差异”的大小。我对黑色毫无怨言——我喜欢它。

**如何使用**

只需从 PyPi 安装 Black 并格式化您的文件。你可以走了！🚀

```
pip install black
# Format everything in my_project/
black my_project 
```

对于 CI/CD 管道，您可能希望检查所有代码是否都用黑色格式化。这就是检查选项的作用:

```
black my_project --check 
```

如果代码没有全部格式化，那么`--check`将无法通过测试。

## 4。覆盖率 Python 的简单代码覆盖率

如果你打算写单元测试，你最好检查一下你是否遗漏了一点。[覆盖率](https://coverage.readthedocs.io/)是一种使用几乎任何现有工具检查代码覆盖率的简单方法。

**为什么好**

测试覆盖，虽然不是银弹，却是维护高质量代码库的好方法。很容易看出项目中最弱的测试区域是什么——只需检查报告！

当你在写新的特性时，一个暗淡的覆盖率报告可以帮助你朝着正确的方向前进。我发现有一个目标数字对我的测试动机很有帮助。

**如何使用**

像这个列表中的其他东西一样，覆盖率是使用`pip` :
从 PyPy 安装的

```
pip install coverage 
```

为了测量源代码的覆盖率，将目录传递给`coverage run` :

```
# With Pytest
coverage run --source my_project pytest
# View the reports!
coverage report 
```

Pytest 还有一个很棒的[插件](https://pypi.org/project/pytest-cov/)，它将与 Pytest 一起运行`coverage`。这很好，因为你可以看到每次测试的覆盖率。😎

## 5。Python 的 MyPy 类型检查

这是我列表中相对较新的内容，但是到目前为止，我喜欢用 [MyPy](http://mypy-lang.org/) 来检查 Python 的类型。如果你的代码(ab)使用了 Python 的动态特性，有时会有“gotchyas ”,但是在大多数情况下，它是正确的。

**为什么好**

拥有良好的单元测试覆盖可以防止很多`TypeErrors`，但是 MyPy 可以通过仔细检查你如何使用你的类型来扩充一个良好的测试套件。Python 中的类型注释也作为文档的一种形式，展示了每个函数和类到底期望什么。

MyPy 唯一的缺点是 Python 是一种动态语言:如果你也想检查你如何使用它们的代码，MyPy 需要在外部模块中定义类型，另外它不处理像动态类创建这样的事情。

**如何使用**

MyPy 也很好用。您应该创建一个`mypy.ini`文件，并从 PyPi 安装它。

```
pip install mypy 
```

使用`mypy`命令对源代码目录运行 MyPy。

```
mypy my_project 
```

如果您没有看到任何东西(并且退出代码是零😉)，祝贺您——它工作正常，您的代码通过了！

## 包装完毕

大型项目中的代码质量可能是一个严重的问题，但是在很多领域，我们的工具可以帮上忙。将这些工具放入您的 CI/CD 管道中，这很容易开始！一个好的 CI/CD 管道可以省去很多麻烦。

我创建了一个 [cookiecutter](https://github.com/madelyneriksen/cookiecutter-python-goodstuff/) ，展示了我如何将所有这些整合到我的项目中的例子。有兴趣的可以去看看！

回头见！干杯。
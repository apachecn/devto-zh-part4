# 使用 setuptools 打包您的 Python 应用程序

> 原文：<https://dev.to/wangonya/using-setuptools-to-package-your-python-app-18p4>

在我们之前的帖子中，我们制作了一个 Hello World！让我们开始点击的应用程序。你可能已经注意到了，我们需要做`python hello-world.py`来运行它。因为我们正在构建 CLI 应用程序，这一点也不酷😅而且 CLI 应用程序也不是这样工作的。我们需要能够运行单个命令，比如:

```
(venv) $ hello
Hello World! 
```

Enter fullscreen mode Exit fullscreen mode

这就是[设置工具](https://setuptools.readthedocs.io/en/latest/)的用武之地。Setuptools 帮助我们捆绑我们的脚本，这样我们可以像安装应用程序(`$ hello`)一样安装和运行它，而不是像脚本(`$ python hello-world.py`)。

> Setuptools 是 Python distutils 的一个增强集合，它允许开发人员更容易地构建和分发 Python 包，尤其是那些依赖于其他包的包。- <small>[文档](https://setuptools.readthedocs.io/en/latest/setuptools.html)</small>

好了，闲聊够了。让我们开始工作吧。我们需要做的第一件事是向我们的项目添加一个新文件，并将其命名为`setup.py`。我们也将把`hello-world.py`重命名为`helloworld.py`(去掉破折号)。当我们到达`setup()`部分时，我会解释为什么这是必要的。我们的项目结构现在应该是这样的:

```
hello-world-cli
    |- helloworld.py  # renamed from hello-world.py
    |- setup.py  # new file 
```

Enter fullscreen mode Exit fullscreen mode

在`helloworld.py`中，移除`if __name__ == '__main__':`部分。代码现在应该是这样的:

```
import click

@click.command()
def hello():
    click.echo('Hello World!') 
```

Enter fullscreen mode Exit fullscreen mode

在`setup.py`文件中，添加代码:

```
from setuptools import setup

setup(
    name='hello-world-cli',
    py_modules=['helloworld'],
    install_requires=[
        'Click',
    ],
    entry_points='''
        [console_scripts]
        hello=helloworld:hello
    ''',
) 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看看每一行代码都做了什么。

```
from setuptools import setup 
```

Enter fullscreen mode Exit fullscreen mode

为了使用它，我们需要从`setuptools`导入`setup`。

接下来，我们调用`setup()`函数并传递一些参数:

```
name='hello-world-cli' 
```

Enter fullscreen mode Exit fullscreen mode

这将是我们 CLI 应用程序的名称。

```
py_modules=['helloworld'] 
```

Enter fullscreen mode Exit fullscreen mode

这告诉`setup()`在哪里找到我们要执行的主模块。我们这里的主文件是`helloworld.py`，所以我们将`helloworld`设置为模块。我们可以保持简单，因为我们现在只有一个文件，但随着我们的应用程序的增长，我们将用`packages=find_packages()`替换`py_modules`。稍后会详细介绍。

```
install_requires=['Click'] 
```

Enter fullscreen mode Exit fullscreen mode

我们的应用程序依赖 Click，所以需要安装它才能工作。需要知道这一点，这样它就可以在打包我们的项目时包含它。`install_requires`接受一个值列表，所以如果我们有更多的依赖项，我们会在这里列出所有的依赖项。

```
entry_points='''
          [console_scripts]
          hello=helloworld:hello
      ''' 
```

Enter fullscreen mode Exit fullscreen mode

这为我们省去了每次都必须用`python ...`运行应用程序的麻烦，并使我们能够只执行`hello`来运行它。

[![entry_points-image](img/c5fa524175bbc8bbcbbe246466f75dad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--clXGLLIK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tdz86n85skubae5ypycq.png)

<small>**需要注意的是，如果我们维持以前的名字** ( `hello-world.py` ) **，** `setup` **在** `entry_points`就失败了。**这是因为 python 模块名不应该有破折号。**T12】</small>

我们现在可以在我们的环境中安装并运行我们的应用程序。要安装它，运行:

```
(venv) $ pip install --editable . 
```

Enter fullscreen mode Exit fullscreen mode

然后执行创建的命令:

```
(venv) $ hello
Hello World! 
```

Enter fullscreen mode Exit fullscreen mode

很漂亮，对吧？😃

在下一篇文章中，我们将仔细看看点击命令、选项和参数，以及我们如何使用它们来使我们的应用程序变得更好。
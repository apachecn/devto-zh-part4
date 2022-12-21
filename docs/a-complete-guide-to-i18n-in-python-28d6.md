# Python 中 i18n 的完整指南

> 原文：<https://dev.to/mblayman/a-complete-guide-to-i18n-in-python-28d6>

这是一个从头到尾的指南，展示了如何为 Python 应用程序进行国际化(i18n)。当我将 i18n 添加到 [handroll](http://handroll.github.io/) 时，我很难找到支持其他语言的明确建议。这是一个解释我如何到达那里的固执的观点。

目录:

*   [标记字符串](#marking)
*   [提取主列表](#extracting)
*   [获得其他语言的翻译](#pot2po)
*   [包装在一起](#packaging)
*   [测试包装](#testing)
*   [疑问？](#questions)

## 概述

要国际化代码，您必须以某种方式处理用户文本字符串。所有的文本字符串必须用一个特殊的函数调用来包装。这个特殊函数将字符串标记为需要翻译的内容。一旦所有的字符串都被标记，i18n 工具就可以扫描你的代码来制作一个主列表。有了主列表，翻译人员可以为每种语言生成一个翻译字符串列表。翻译后的字符串被添加回 Python 代码中，并打包成一个漂亮的翻译成品。然后你可能想用[测试一下](https://www.mattlayman.com/blog/2013/test-your-packaging/)。

这是很多东西，但是我将详细解释该过程的每个部分。出于本指南的目的，我的所有示例代码都将引用`handroll`包。如果这是您的 Python 代码，请用您的顶级 Python 包的名称替换`handroll`。

## 标记字符串

我在概述中提到的特殊功能看起来像`_('Hello World')`。该功能来自`gettext`模块。Python 使用 GNU gettext 进行翻译，所以让我们看看在`handroll/i18n.py`中创建`_`函数的 handroll 代码。

```
import gettext
import os

localedir = os.path.join(os.path.abspath(os.path.dirname(__file__)), 'locale')
translate = gettext.translation('handroll', localedir, fallback=True)
_ = translate.gettext 
```

这一小块代码使用一个`locale`目录作为翻译字符串的源来创建一个翻译对象。您的`locale`目录还不存在，但是它是您的应用程序在运行时查找翻译的地方。从翻译对象，我做了一个`translate.gettext`的`_`别名。这个下划线并不特别，但是，它是 Python 社区使用的这个函数的常规名称。

我希望现在有足够的内容来理解运行时会发生什么。重要的想法是`'Hello World'`就像一把钥匙。当代码执行时，这个“键”将被用来在语言环境数据中查找匹配的翻译。如果缺少翻译，`_`的返回值将是翻译后的字符串或原始字符串。

### `_`和`format`弦

如果对字符串使用`format`的话，你很快就会遇到一个奇怪的问题，那就是在哪里关闭`_`括号。这里举个例子来说明一下。

```
_('Close the underscore function first. A {number}').format(number=42) 
```

## 提取主列表

所有的字符串都标有`_()`，是时候将它们提取到一个主列表中了。Gettext 将这个主列表称为`Portable Object Template` ( `POT`)文件。为了生成 POT 文件，我求助于一个叫做 [Babel](http://babel.pocoo.org/) 的工具。Babel 旨在帮助开发者以多种方式处理 i18n 问题。它很酷，有很多特性，但是对于本指南，我将重点关注它的 gettext 支持。

Babel 用新命令扩展了一个`setup.py`文件。(Python 打包超出了本指南的范围，所以如果`setup.py`对你来说完全陌生，请查看[发布的 Python 模块](https://docs.python.org/2/distutils/introduction.html)以获得更多信息。)

要生成 POT 文件，运行`python setup.py extract_messages`。您将需要一些类似于
的设置

```
[extract_messages]
input_dirs = handroll
output_file = handroll/locale/handroll.pot 
```

## 获取其他语言的翻译

现在您已经生成了一个 POT 文件，您已经准备好获得字符串的翻译版本了。记住锅里的 *T* 是给*模板*用的。翻译后的文件称为 PO 文件，翻译人员使用 POT 文件作为起点来生成他们的 PO 文件。通过手动创建 PO 文件来进行翻译是可能的，但这很繁琐。在这一点上，我转向了一个叫做 [Transifex](https://www.transifex.com/) 的服务。

Transifex 致力于简化翻译。它对开源项目是免费的，而且非常容易使用。我为 handroll 建立了一个项目，配置了 Transifex 来“观察”POT 文件的变化，翻译者可以很容易地翻译项目中的所有字符串。Transifex 有一个 API，可以让我在需要的时候将 PO 文件拉回到存储库中。这个脚本有点太长了，不能放在这个帖子里，但是你可以在 GitHub 上[看看。](https://github.com/handroll/handroll/blob/master/transifex.py)

该脚本做的一件重要的事情是将 PO 文件存储在`locale`目录中的特定结构中。Gettext 期望一个类似于`<language>/LC_MESSAGES/handroll.po`的订单。

## 包装在一起

翻译好 PO 文件后，就该打包翻译数据了。设置过程包括`MANIFEST.in`中的额外数据。添加一行类似下面的代码，确保所有的`locale`数据都放入包存档中。

```
recursive-include handroll/locale * 
```

到目前为止，为了解释清楚，我撒了一点谎。Gettext 不直接使用 PO 文件来查找翻译。事实是 PO 文件必须编译成二进制机器对象(MO)文件(为了更快的速度)。同样，我们可以向巴别塔求助。Babel 有一个`compile_catalog`命令，可以把 PO 编译成 MO。可以用`python setup.py compile_catalog`运行。

它需要如下设置:

```
[compile_catalog]
domain = handroll
directory = handroll/locale 
```

因为没有 MO 文件翻译将无法工作，所以我扩展了`setup.py`以便`sdist`命令将总是运行`compile_catalog`。

```
from setuptools.command.sdist import sdist

class Sdist(sdist):
    """Custom ``sdist`` command to ensure that mo files are always created."""

    def run(self):
        self.run_command('compile_catalog')
        # sdist is an old style class so super cannot be used.
        sdist.run(self) 
```

如果你这样做，不要忘记在你的`setup`呼叫中添加`cmdclass={'sdist': Sdist}`和`setup_requires=['Babel']`。

此时，运行`python setup.py sdist`应该会为您的项目创建一个带有翻译的 tarball。你快完成了！

## 测试出包

测试翻译并不容易。如果你多做一点，你就会知道来自不同文化的人都可以享受你的软件。但是翻译测试并不容易，因为您需要测试运行代码中的每个字符串。对于 handroll 来说，这意味着我必须达到 100%的测试覆盖率，以获得那些真正奇怪的角落案例。

测试的原因是不正确翻译的字符串会破坏您的代码。如果你有一个类似于`_('Hello {name}!').format(name='Johnny')`的格式字符串，而翻译人员犯了一个类似于`'¡Hola {nombre}!'`的错误，那么对于西班牙语用户来说，代码将会中断。

```
>>> '¡Hola {nombre}!'.format(name='Johnny')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'nombre' 
```

为了测试这些字符串，您需要两样东西:MO 文件和`LANGUAGE`环境变量。让我们假设你使用[鼻子](https://nose.readthedocs.org/en/latest/)测试跑步者。要测试您的西班牙语支持，您可以运行:

```
$  LANGUAGE=es nosetests
.................................. ----------------------------------------------------------------------
Ran 34 tests in 1.440s

OK 
```

在你使用的任何持续集成系统中，通过你支持的每种语言运行你的单元测试，给你自己一些信心，翻译没有破坏软件。 [handroll tox.ini](https://github.com/handroll/handroll/blob/master/tox.ini) 提供了一个比较的例子。 [Tox](https://tox.readthedocs.org/en/latest/) 对这种东西来说太棒了。

## 提问？

在本指南中，我尽最大努力记录了我为项目国际化所做的一切。希望所有的具体细节都有助于你看到如何翻译自己的项目。

这篇文章最初发表在 mattlayman.com 的上。
# Python 中的仅位置参数

> 原文：<https://dev.to/deepsource/positional-only-arguments-in-python-108e>

在即将到来的 [3.8 版本](https://deepsource.io/blog/python-3-8-whats-new/)中，对 Python 语言进行了许多新的改进，其中包括在函数定义中使用`/`标记来指定仅位置参数的能力。对语法的这一补充有利于提高性能，并支持更好的 API 设计。让我们通过例子来看看位置唯一论元背后的动机以及如何使用它。

## 背景

Python 中只有关键字的参数有了`*`标记，为只有位置的参数增加了`/`标记提高了语言的一致性。对于位置或关键字参数，混合调用约定并不总是可取的。考虑这些例子:

*   有些函数参数已经有语义:`namedtuple(typenames, field_names, …)`
*   参数名没有真正的外部含义:`min()`的`arg1`、`arg2`、……等

如果用户开始使用关键字参数，库作者不能重命名该参数，因为这将是一个突破性的改变。在`min()`的情况下，参数的名称不提供内在值，并迫使作者永远保留其名称，因为调用者可能会将参数作为关键字传递。这个问题通过仅位置参数来解决。此外，解析和处理仅位置参数的速度更快。

## 如何使用仅位置参数

要将参数指定为仅位置参数，应该在函数定义中所有这些参数的后面添加一个`/`标记。举个例子:

```
def pow(x, y, /, mod=None):
    r = x ** y
    if mod is not None:
        r %= mod
    return r 
```

以下情况适用:

*   `/`*左侧*(此处为`x`和`y`)的所有参数只能按位置传递。
*   `mod`可以按位置传递，也可以用关键字传递。

```
>>> pow(2, 10)  # valid >>> pow(2, 10, 17)  # valid >>> pow(2, 10, mod=17)  # valid >>> pow(x=2, y=10)  # invalid, will raise a TypeError >>> pow(2, y=10)  # invalid, will raise a TypeError 
```

再比如:

```
def table_format(items, separator=',', /, prettify=False):
    pass 
```

*   一旦用默认值指定了仅位置参数，下面的仅位置参数和位置或关键字参数也需要有默认值(在本例中为`prettify`)。
*   没有默认值的仅位置参数是`required`仅位置参数(本例中为`items`)。

### 泛格

一个函数定义，带有各种风格的参数传递变量，看起来像这样:

```
def f(pos1, pos2, /, pos_or_kwd, *, kwd1, kwd2):
      -----------    ----------     ----------
        |             |                  |
        |        Positional or keyword   |
        |                                - Keyword only
         -- Positional only 
```

## 何时使用仅位置参数

*   只有当名字无关紧要或者没有意义，并且只有几个参数总是以相同的顺序传递时，才使用位置。
*   只有当名字有意义并且函数定义通过名字显式化更容易理解时，才使用关键字。

在 PEP 570 中提出了仅位置参数，值得看一看文档以更详细地理解该特性。

* * *

*最初发布于 [DeepSource 博客](https://deepsource.io/blog/python-positional-only-arguments/)。加入 reddit 的讨论。*
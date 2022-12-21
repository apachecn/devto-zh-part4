# Python 类型提示指南:Python 中的类型提示

> 原文：<https://dev.to/kite/guide-type-hinting-in-python-1p1l>

# 指南:Python 中的类型提示

从版本 3.5 开始，Python 支持类型提示:代码注释，通过额外的工具，可以检查您是否正确地使用了代码。

## 简介

随着 3.5 版本的发布，Python 引入了类型提示:代码注释，通过额外的工具，可以检查您是否正确地使用了代码。

Python 的老用户可能会害怕新代码需要类型提示才能正常工作，但是我们不必担心:Guido 自己在 PEP 484 中写道，“运行时不会进行类型检查。”

提出这个特性主要是为了开放 Python 代码，使静态分析和重构更加容易。

对于数据科学和数据科学家来说，类型提示是非常宝贵的，原因如下:

*   只要看一下签名，即函数定义的第一行，理解代码就容易多了；

*   它创建了一个可以用类型检查器检查的文档层，也就是说，如果你改变了实现，但是忘记了改变类型，类型检查器就会(希望)对你大喊大叫。

当然，正如文档和测试的情况一样，这是一项投资:它在开始时会花费你更多的时间，但是从长远来看，它会为你(和你的同事)节省很多时间。

注意:类型提示也已经移植到 Python 2.7(也称为传统 Python)。但是，该功能需要注释才能工作。此外，没有人应该在 2019 年使用传统 Python:它不太漂亮，在停止接受任何类型的支持之前只有几个月的更新。

## 类型入门

这篇文章的代码可以在 [Kite 的 Github 库](https://github.com/kiteco/kite-python-blog-post-code/tree/master/python-typing)中找到。

类型提示的 hello world 是:

```
# hello_world.py def hello_world(name: str = 'Joe') -> str:
    print(f'Hello {name}') 
```

我们在这里添加了两个类型提示元素。第一个是名字后面的`: str`，第二个是签名末尾的`-> str`。

语法如您所料:我们将 name 标记为类型`str`，并指定`hello_world`函数应该输出一个`str`。如果我们使用我们的函数，它会做它所说的:

```
> hello_world(name='Mark')
'Hello Mark' 
```

由于 Python 仍然是一种动态的未经检查的语言，我们仍然会搬起石头砸自己的脚:

```
> hello_world(name=2)
'Hello 2' 
```

发生什么事了？正如我在引言中所写的，在运行时不进行类型检查。

因此，只要代码没有引发异常，事情就会继续顺利进行。

那么你应该如何处理这些类型定义呢？嗯，你需要一个类型检查器，或者一个 IDE 来读取和检查你的代码中的类型(例如 PyCharm)。

类型检查你的程序
至少有四种主要的类型检查器实现: [Mypy](http://mypy-lang.org/) 、 [Pyright](https://github.com/Microsoft/pyright) 、 [pyre](https://pyre-check.org/) 和 [pytype](https://github.com/google/pytype) :

*   Mypy 是由 Python 的创始人吉多·范·罗苏姆等人积极开发的。
*   Pyright 由微软开发，与他们优秀的 Visual Studio 代码集成得非常好；
*   Pyre 由脸书开发，目标是更快(尽管 mypy 最近变得更快)；
*   Pytype 由 Google 开发，除了像其他软件一样检查类型，它还可以对未注释的代码进行类型检查(并添加注释)。因为我们想从 Python 的角度关注如何使用类型，所以在本教程中我们将使用 Mypy。我们可以使用`pip`(或者您选择的软件包管理器)来安装它:

```
$ pip install mypy
$ mypy hello_world.py 
```

更高级的类型
原则上，所有 Python 类都是有效类型，意味着你可以使用`str`、`int`、`float`等。使用 dictionary、tuples 等也是可能的，但是您需要从类型模块中导入它们。

```
# tree.py from typing import Tuple, Iterable, Dict, List, DefaultDict
from collections import defaultdict

def create_tree(tuples: Iterable[Tuple[int, int]]) -> DefaultDict[int, List[int]]:
    """
    Return a tree given tuples of (child, father)

    The tree structure is as follows:

        tree = {node_1: [node_2, node_3], 
                node_2: [node_4, node_5, node_6],
                node_6: [node_7, node_8]}
    """
    tree = defaultdict(list) 
    for child, father in tuples:
        if father:
            tree[father].append(child)
    return tree

print(create_tree([(2.0,1.0), (3.0,1.0), (4.0,3.0), (1.0,6.0)]))
# will print
# defaultdict( 'list'="">, {1.0: [2.0, 3.0], 3.0: [4.0], 6.0: [1.0]} 
```

虽然代码很简单，但它引入了一些额外的元素:

首先，`Iterable`类型为`tuples`变量。该类型表示对象应该符合`collections.abc.Iterable`规范(即实现`__iter__`)。这是必要的，因为我们在 for 循环中迭代元组；
我们在容器对象中指定类型:Iterable 包含元组，元组由成对的`int`组成，等等。

好，让我们试着打字检查一下！

```
$ mypy tree.py
tree.py:14: error: Need type annotation for 'tree' 
```

啊哦，发生什么事了？基本上 Mypy 在抱怨这一行:

```
tree = defaultdict(list) 
```

虽然我们知道返回类型应该是`DefaultDict[int, List[int]]`，但是 Mypy 不能推断 tree 确实是那种类型。我们需要通过指定树的类型来解决这个问题。这样做可以类似于我们在签名中的做法:

```
tree: DefaultDict[int, List[int]] = defaultdict(list) 
```

如果我们现在再次重新运行 Mypy，一切正常:

```
$ mypy tree.py
$ 
```

## 键入别名

有时我们的代码会一遍又一遍地重用相同的复合类型。在上面的例子中，Tuple[int，int]可能就是这样一种情况。为了使我们的意图更加清晰(并缩短我们的代码)，我们可以使用类型别名。类型别名非常容易使用:我们只需将一个类型赋给一个变量，并将该变量作为新类型使用...

```
Relation = Tuple[int, int]

def create_tree(tuples: Iterable[Relation]) -> DefaultDict[int, List[int]]:
    """
    Return a tree given tuples of (child, father)

    The tree structure is as follow:

        tree = {node_1: [node_2, node_3], 
                node_2: [node_4, node_5, node_6],
                node_6: [node_7, node_8]}
    """
    # convert to dict
    tree: DefaultDict[int, List[int]] = defaultdict(list) 
    for child, father in tuples:
        if father:
            tree[father].append(child)

    return tree 
```

## 仿制药

有经验的静态类型语言程序员可能已经注意到，将一个关系定义为一个整数元组有点限制。难道`create_tree`不能使用浮点、字符串或者我们刚刚创建的临时类吗？

原则上，没有什么可以阻止我们这样使用它:

```
# tree.py from typing import Tuple, Iterable, Dict, List, DefaultDict
from collections import defaultdict

Relation = Tuple[int, int]

def create_tree(tuples: Iterable[Relation]) -> DefaultDict[int, List[int]]:
    ...

print(create_tree([(2.0,1.0), (3.0,1.0), (4.0,3.0), (1.0,6.0)]))
# will print
# defaultdict( 'list'="">, {1.0: [2.0, 3.0], 3.0: [4.0], 6.0: [1.0]}) 
```

然而，如果我们询问 Mypy 对代码的看法，我们会得到一个错误:

```
$ mypy tree.py
tree.py:24: error: List item 0 has incompatible type 'Tuple[float, float]'; expected 'Tuple[int, int]'
... 
```

Python 中有一种方法可以解决这个问题。它被称为 TypeVar，它通过创建一个不需要假设的泛型类型来工作:它只是在我们的模块中修正它。用法非常简单...

查看代码- [指南:在 Kite 的博客上用 Python 3.5](https://kite.com/blog/python/type-hinting/) 输入提示。
*Giovanni Lanzani* 是 GoDataDriven 的学习与发展总监。
# 享受数据类和抽象基类的乐趣

> 原文：<https://dev.to/ivergara/having-fun-with-dataclasses-and-abstract-base-classes-29a8>

Python 因其工作所需的小模板而闻名。但是，当必须定义一大堆相对简单的方法来获得类的期望行为时，即使是 Python 也会变得有点麻烦。
在本文中，我们将探讨如何将 [`dataclases`](https://docs.python.org/3/library/dataclasses.html) 与 Python 中标准库的 [`abc`](https://docs.python.org/3.7/library/abc.html) 和 [`collections.abc`](https://docs.python.org/3.7/library/collections.abc.html) 模块结合起来。我假设你知道/明白什么是`abc`、`collections.abc`和`dataclases`。有了最后两个，你就可以得到很多免费的行为！

如果你不了解抽象基类，那么我强烈推荐你分别查看类似于[这个](http://blog.thedigitalcatonline.com/blog/2016/04/03/abstract-base-classes-in-python/)和[这个](http://stupidpythonideas.blogspot.com/2015/07/creating-new-sequence-type-is-easy.html)的文章，分别针对`abc`和`abc.collections`。同样，如果你不知道`dataclasses`为什么有趣以及它们的优点，你应该看看[这篇](https://realpython.com/python-data-classes/)别的文章。或者，如果你喜欢一些更直观的指南，请查看本讲座。花时间学习这些工具，这是值得的。就我个人而言，自从我发现了`abc`和`collections.abc`模块，我一直在尽可能地使用它们。

当我看到 Python 3.7 的标准库中包含了`dataclass`模块时，我告诉自己我想使用它。能够进一步减少 Python 中的样板文件似乎是个好主意。当然，我可能已经用 [`attrs`](https://www.attrs.org/) 达到了基本相同的效果，但是当我尝试的时候感觉不自然。那很可能是因为我缺乏经验。

因此，很明显，我最终会在某个时候混合抽象类、抽象集合和数据类。不幸的是，我没有花时间在工作中重构代码来达到这个效果。为了解决这个问题，我决定用一个玩具例子探索一下`abc+dataclasses`和`abc.collections+dataclasses`的组合，看看这种组合是否简单。由于我没有找到任何混合这两个概念的文章(并不是说我看了太多)，我决定写一写它。

现在，弄脏我们的手指。首先让我们使用`pipenv`来创建一个环境(就像你应该做的那样来保持一些环境卫生)。这样做可能有点大材小用，但我发现这比从头开始创建虚拟环境要容易得多。因此，我们如下初始化一个 Python 3.7 环境`pipenv --python 3.7`。

为了指导这个实验，我们将编写一个简单的测试。你当然可以跳过这一步，在选择的 REPL 中手动处理代码，在这种情况下，我建议你在任何情况下都可以自由地探索和发现你的用例，但是有测试会使这个过程更容易。安装`pytest`，通过执行`pipenv install pytest`来运行测试。注意，我没有为此使用单独的`dev`环境，因为这只是一个实验环境。现在，我们可以通过使用`pipenv shell`来激活虚拟环境。

我能想到的最简单的测试是，如果你试图直接实例化抽象类`Base`的话，它应该抛出一个`TypeError`异常。

```
# test_demo.py import pytest
from demo import Base

def test_abstract_base_class():
    with pytest.raises(TypeError):
        Base() 
```

Enter fullscreen mode Exit fullscreen mode

在执行`pytest`之前，由于它会失败，我们可以基于`dataclasses`和`abc`快速编写一个实现。因此，这个类用`@dataclass`来修饰，并且继承自`abc.ABC`。此外，它将使用一个`__post_init__`和一个`process`方法定义一个类型为`str`的字段`a`，最后一个方法被定义为 abstract。

```
# demo.py import abc
from dataclasses import dataclass

@dataclass
class Base(abc.ABC):
    a: str

    def __post_init__(self):
      self.a = self.a.upper()

    @abc.abstractmethod
    def process(self) -> str:
        pass 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止一切顺利。由于`__post_init__`方法不是一个抽象方法，它将在从`Base`继承的每个类中执行。

现在是时候创建一个实现抽象类的类了。正如在[引用](https://docs.python.org/3/library/dataclasses.html#inheritance)中所描述的，为了让`dataclasses`中的继承工作，两个类都必须被修饰。
在这种情况下，实现将定义另一个字段`str`类型的`b`，重新实现`__post_init__`方法，并实现抽象方法`process`。

```
# demo.py @dataclass
class Implementation(Base):
    b: str

    def __post_init__(self):
        super().__post_init__()
        self.b = self.b.lower()

    def process(self) -> str:
        return f"{self.a}  {self.b}" 
```

Enter fullscreen mode Exit fullscreen mode

我们重新实现了`__post_init__`方法，只是为了表明我们可以轻松地覆盖更复杂的用例。这迫使我们调用`super().__post_init__()`来获得基类的 post 初始化。现在我们可以在测试中测试这个新类的行为，如下所示。

```
# test_demo.py from demo import Implementation

def test_implementation():
    implemented_instance = Implementation("Pythonic", "Musings")
    assert isinstance(implemented_instance, Base)
    assert implemented_instance.process() == "PYTHONIC musings" 
```

Enter fullscreen mode Exit fullscreen mode

在这个测试中，第一个`assert`是确保`Implemented`类确实是`Base`的一个实例。我们可以信任 Python，但是因为这篇文章有教育目的，所以最好明确我们的期望。

数据类的一个很大的优点是你必须做类型注释。因此，我们可以看到如果我们在它上面运行类似于`mypy`的类型检查器会发生什么。执行`pipenv install mypy`然后`mypy .`我们得到下面的

```
demo.py:7: error: Only concrete class can be given where "Type[Base]" is expected
... 
```

Enter fullscreen mode Exit fullscreen mode

只有一个错误！这比我们预料的更糟。搜索了一下，我发现下面的[期](https://github.com/python/mypy/issues/5374)在讨论这种情况。否则所有检查都很好，我们对`mypy`的边缘情况不感兴趣，所以我们可以忽略它，或者在您作为 CI 的一部分运行`mypy`的情况下让它保持沉默。

现在我们终于可以将`dataclasses`和`collections.abc`模块结合起来了。这种组合很棒，因为两个模块都提供了减少样板文件的方法，同时也使意图非常清晰。为了简单起见，它将是一个带有类型为`List`的字段`c`和自定义方法`capitalize`的直接容器。

```
# demo.py import collections.abc
from typing import List

@dataclass
class Derived(collections.abc.Container):
    c: List[str]

    def __contains__(self, value):
        return value in self.c

    def capitalize(self) -> List[str]:
        return list([e.upper() for e in self.c]) 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们得到了结合两种样板缩减方法的全部能力。
这个例子，绝不显示`collections.abc`的所有潜力，因为我们选择了最简单的集合。但这里只是为了说明与数据类的结合是可行的。我真的推荐使用`collections.abs`模块，因为它可以让你封装很多东西，并带来更好的设计。

为了测试它，我们可以使用下面的代码

```
# test_demo.py from demo import Derived

def test_derived():
    instance = Derived(["Hi", "Bye"])

    for element in ["Hi", "Bye"]:
        assert element in instance

    assert instance.capitalize() == ["HI", "BYE"] 
```

Enter fullscreen mode Exit fullscreen mode

第一个断言检查我们的方法是否按预期工作。第二组断言，即那些在`for`循环中的断言，只有一个教学目的，因为我们最终测试的是`collections.abs`正在工作。尽管有时，这种测试对于符合规范是有效的。总之，在这里不去探讨认识论的检验。在这里，我们再次看到`collections.abs`和`dataclasses`的组合刚刚好。

我们对`Derived`类的实现非常粗糙。作为一个练习，试着将`capitalize`方法转换成一个`classmethod`，它接受一个`Derived`的实例，并返回一个带有大写元素的类的实例。这将改善人机工程学，因为以这样一种隐含的方式返回一个`list`是不太一致的。正确的类型注释加分！(提示:查 PEP 563。)

我们以此结束本文。毫不奇怪，`dataclasses`模块与`abc`和`collections.abs`配合得非常好。当然，在这次探索之后，我将开始在未来使用这些组合，并通过查看旧代码来利用它。

teaser:Python 中还有一个我喜欢使用的东西，即“@property”装饰器。就我个人而言，我也想知道这和`dataclasses`有什么关系，幸运的是已经有人在这里讲述了他们的故事[。剧透预警，有大团圆结局；).尽管在使用它的时候，我发现了一些边缘情况，在这些情况下，事情不能很好地工作。我希望更详细地探讨它，并展示一些替代方案/解决方案。](https://blog.florimondmanca.com/reconciling-dataclasses-and-properties-in-python)
# 深入了解数据类

> 原文：<https://dev.to/ivergara/deeper-into-dataclasses-6fd>

通常，使用 Python 中的`dataclasses`模块的例子在使用其特性方面相当简单。这本身完全没问题，但有时实现可能会非常繁琐和麻烦。然而，`dataclasses`模块提供了变得更聪明的方法，这一点很少被提及。通过这篇文章，我想改变这种情况。因此，本文不讨论何时以及如何使用它们。互联网上有很多关于这方面的资料。

下面的代码表示一个 3D 点，它可以添加到另一个点，并按元素乘以一个数字。(注意:在我看来，这使得这个对象更像一个向量)。一个额外的特性是，它还支持通过`__iter__`方法进行迭代和解包，使得点和数字[可交换](https://en.wikipedia.org/wiki/Commutative_property)。

```
# Baseline solution from dataclasses import astuple, dataclass

@dataclass
class Point:
    x: float
    y: float
    z: float

    def __add__(self, other):
        x1, y1, z1 = self
        x2, y2, z2 = other
        return Point(x1+x2, y1+y2, z1+z2)

    def __sub__(self, other):
        x1, y1, z1 = self
        x2, y2, z2 = other
        return Point(x1-x2, y1-y2, z1-z2)

    def __mul__(self, scalar):
        x, y, z = self
        return Point(scalar*x, scalar*y, scalar*z)

    def __rmul__(self, scalar):
        return self.__mul__(scalar)

    def __iter__(self):
        return iter(astuple(self)) 
```

Enter fullscreen mode Exit fullscreen mode

这是一个很好的实现，但是即使使用了 point 可以解包的事实，也是相当繁琐和重复的。为每个方法键入`x1, y1, z1 = self`并不理想。此外，如果我们想在 2D、4D 或 6D 也有积分呢？这很简单，但是容易出错。我们必须在相关方法中添加/删除属性/字段定义以及对它们的所有引用。在这个特殊的例子中，修改了 6 行。第一部分很容易，第二部分(非常)烦人。我们可以做得稍微好一点，如果我们想解决其他方面的问题，只需关注第一部分。

## 利用`dataclasses`自省

让我们稍微聪明一点，使用更多在`dataclasses`模块中可用的工具。特别是，函数`fields()`公开了在`dataclass`中定义的字段。因此，我们不必在不同的操作方法中命名该点的每个坐标，而是可以对它们进行迭代。

```
# Introspection based solution from dataclasses import astuple, dataclass, fields

@dataclass
class Point:
    x: float
    y: float
    z: float

    def __add__(self, other):
        return Point(*(getattr(self, dim.name)+getattr(other, dim.name) for dim in fields(self)))

    def __sub__(self, other):
        return Point(*(getattr(self, dim.name)-getattr(other, dim.name) for dim in fields(self)))

    def __mul__(self, other):
        return Point(*(getattr(self, dim.name)*other for dim in fields(self)))

    def __rmul__(self, other):
        return self.__mul__(other)

    def __iter__(self):
        return iter(astuple(self)) 
```

Enter fullscreen mode Exit fullscreen mode

为了理解它是如何工作的，我将重点介绍`__add__`方法。有相当多的东西需要打开。核心是对`fields()`函数的调用，它返回 3 个`field`对象的元组，一个`field`对象是一个`dataclass`如何表示一个属性。你可以(也应该)在 REPL 中检查它，这可以在类本身或它的一个实例上完成。因为我们有一个元组，所以我们可以迭代它并访问每个属性的名称。

```
>>> for field in fields(Point):
...     print(field.name)
...
x
y
z 
```

Enter fullscreen mode Exit fullscreen mode

然后，下一步是使用它从实例中获取与每个属性相关的值，如下所示

```
>>> p = Point(1,2,3)
>>>list(getattr(p, field.name) for field in fields(p))
[1,2,3] 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在被操作的两个点实例之间进行操作，我们将生成器表达式解包到`Point`初始化
的参数中

```
Point(*(getattr(self, dim.name)+getattr(other, dim.name) for dim in fields(self)) 
```

Enter fullscreen mode Exit fullscreen mode

最后一步是将其作为`__add__`方法的返回值，然后对其他方法实施相同的策略。这样，我们就完成了第一步，消除了当我们改变 point 类的维数时必须接触每个方法的烦恼。额外的收获是，我们还稍微减少了代码的总行数。

## 一种替代方案

我不得不承认，使用这种级别的`dataclass`内省可能有点麻烦，尤其是通过使用`getattr`函数。如果我们没有实现`__iter__`方法，这将是解决方案。但是因为我们支持迭代器协议，我们可以做一些更聪明的事情。因此，我们可以迭代点对象本身，而不必迭代已定义的字段！

```
# Iterator based solution import operator

from dataclasses import astuple, dataclass, fields

@dataclass
class Point:
    x: float
    y: float
    z: float

    def __iter__(self):
        return iter(astuple(self))

    def __add__(self, other):
        return Point(*(operator.add(*pair) for pair in zip(self,other)))

    def __sub__(self, other):
        return Point(*(operator.sub(*pair) for pair in zip(self,other)))

    def __mul__(self, other):
        return Point(*(operator.mul(*pair) for pair in zip(self,other)))

    def __rmul__(self, other):
        return self.__mul__(other) 
```

Enter fullscreen mode Exit fullscreen mode

为了突出`__iter__`方法在这个解决方案中扮演的关键角色，我把它移到了顶部。对于其他部分，代码应该是不言自明的。我要说的是，使用在`operator`模块中定义的函数也使代码更加清晰。

## 代码质量度量

最近，我也对代码质量度量感兴趣。我有一个关于量化代码质量的标准度量的假设，也就是说，它们不适合像 Python 这样的动态语言。特别是像装饰者这样的功能。

让我们探讨一下之前提到的三个解决方案的一些统计数据，以及一个没有使用`dataclasses` (classic)的解决方案，这个解决方案没有显示出来，但是很容易从基于数据类的简单解决方案中获得。

|  | SLOC | 大调音阶的第三音 | 军阶 |
| --- | --- | --- | --- |
| 经典的 | Twenty-four | Fifty-three point four one | A |
| 基线 | Twenty-one | Fifty-four point two eight | A |
| 反省 | Sixteen | Sixty point two two | A |
| 迭代程序 | Seventeen | One hundred | A |

不用深入研究，可维护性指数随着我们在不同的实现中的移动而增加。这个结果是我直觉上预料到的。令人震惊的是基于迭代器的解决方案的值为 100。这保证了稍后对此进行更深入的探究，因为这似乎不太可能是`radon`库中的一个错误，而且现在我对这个主题太无知了，无法知道为什么会这样。

不知何故，我希望在经典和基线数据类解决方案之间有一个更重要的步骤。但是考虑到我们实现的方法是相同的，并且我们不必编写的方法(`__init__`、`__eq__`、`__repr__`)相当简单，所以它们没有太大的不同是可以理解的。

## 表现

在我的笔记本电脑上使用`%timeit`,我运行了一个添加两个点
的快速基准测试

```
>>> p1 = Point3D(1,2,3)
>>> p2 = Point3D(4,5,6)
>>> %timeit p1+p2 
```

Enter fullscreen mode Exit fullscreen mode

对于本文中实现的三个解决方案，结果如下

|  | 意思(∞) | 标准 |
| --- | --- | --- |
| 基线 | Thirty-three | 5.18 秒 |
| 反省 | Six point zero four | 450 纳秒 |
| 迭代程序 | Thirty point four | 3.34 秒 |

我们可以说，基于内省的解决方案比其他两种解决方案更具性能。更好地理解基于内省(迭代器)的解决方案的成功(失败)来自哪里是很有趣的。

无论如何，这个例子显示了一些有趣的东西，我们可以在提高性能的同时提高可维护性指数。这个事实不一定是已知的，因为通常性能是以牺牲可读性为代价的。

## nD 分呢？

但是情况仍然可以改善。假设你希望 2D 和 3D point 能够共存。我们可以复制并粘贴整个定义，给每个类一个不同的名称，并确保拥有正确数量的属性。但是那将是极其愚蠢的，我们可以(并且应该)使用继承(见[以前的文章](https://ivergara.github.io/ABC-and-dataclasses.html)探索抽象基类和数据类)并且重用所有操作的代码，因为我们只是使它们独立于点所在的维度。既然我们正在探索`dataclasses`，我将回到第一个解决方案。

```
from abc import ABC

from dataclasses import astuple, dataclass, fields

@dataclass
class BasePoint(ABC):

    def __add__(self, other):
        return self.__class__(*(getattr(self, dim.name)+getattr(other, dim.name) for dim in fields(self)))

    def __sub__(self, other):
        return self.__class__(*(getattr(self, dim.name)-getattr(other, dim.name) for dim in fields(self)))

    def __mul__(self, other):
        return self.__class__(*(getattr(self, dim.name)*other for dim in fields(self)))

    def __rmul__(self, other):
        return self.__mul__(other)

    def __iter__(self):
        return iter(astuple(self))

@dataclass
class Point2D(BasePoint):
    x: float
    y: float

@dataclass
class Point3D(BasePoint):
    x: float
    y: float
    z: float 
```

Enter fullscreen mode Exit fullscreen mode

太好了，我们现在不费吹灰之力就可以在任何维度上获得点数！

## 一厂之分

但是有没有比这更简单的方法呢？`dataclasses`模块有一个叫做`make_dataclass`的漂亮函数，顾名思义，它根据参数生成数据类。

我们可以尝试在 1D 创建一个点

```
>>> Point1D = make_dataclass('Point1D', [('x',float)], bases=(BasePoint,))
>>> Point1D(1)
Point1D(x=1) 
```

Enter fullscreen mode Exit fullscreen mode

与用正常的方式定义类相比，这似乎不是一个大的胜利。但是如果我们想在 5 维空间中创建一个奇异点呢？嗯，首先我们创建一个包含字段名和类型的元组列表，然后我们对它使用`make_dataclass`。

```
>>> dims = 5
>>> fields_definition = ((f'x{i}', float) for i in range(dims))
>>> Point5D = make_dataclass('Point5D', fields_definition, bases=(BasePoint,))
>>> Point5D(*range(5))
Point5D(x0=0, x1=1, x2=2, x3=3, x4=4) 
```

Enter fullscreen mode Exit fullscreen mode

我用一种更“数学”的符号从命名`xyz`转移到`x{i}`，这对于计算机来说也更好。

这为创建整个系列的点做好了准备。为此，我们创建一个函数来创建它们(一个工厂)

```
def PointFactory(dim):
     fields_definition = ((f'x{i}', float) for i in range(dim))
     return make_dataclass(f'Point{dims}D', fields_definition, bases=(BasePoint,)) 
```

Enter fullscreen mode Exit fullscreen mode

利用这个工厂，可以很容易地创建一系列表示不同维度的点的类

```
>>> point_classes = [PointFactory(dim) for dim in range(5)]
>>> point_classes
[<class 'abc.Point0D'>, <class 'abc.Point1D'>, <class 'abc.Point2D'>, <class 'abc.Point3D'>, <class 'abc.Point4D'>]
>>> point_classes[3](1,2,3)
Point3D(x0=1, x1=2, x2=3) 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

除了`dataclasses`模块提供的样板文件缩减之外，它还提供了一些强大的工具来处理它们。作为一个例子，我展示了如何创建一个 n 维点工厂。

此外，我发现使用`dataclasses`的内省机制会产生更高性能的代码。这并不是本文的目标，但是得到提升总是好的。请记住，**内省**，在这种情况下，可能是一个稍微错误的术语，因为它会使人们相信它应该是性能较差的，特别是那些来自 Go 的人。

在看到每个实现的性能之后，问题出现了，基于迭代器的方法是否可以通过变得更智能来改进。至少我从`import operator`到`from operator import add, mul, sub`的第一次探索性尝试没有显示出任何变化。也许这对读者来说是一个很好的练习；)

### 鸣谢

我要感谢 Nour Faroua 对简化代码所做的贡献，感谢 Bryan Reynaert 对文章的组织、解释和语言进行了全面的审查和改进。
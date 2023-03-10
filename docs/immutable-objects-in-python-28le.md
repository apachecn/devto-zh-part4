# Python 中的不可变对象

> 原文：<https://dev.to/kabisasoftware/immutable-objects-in-python-28le>

为了使程序易于推理，我试图避免副作用，并致力于使用不可变对象的函数式编程风格。我很高兴用几个 CPU 周期来换取对脑力需求的降低。

因为我们在这里谈论的是 Python，而且[我们都是负责任的用户](https://docs.python-guide.org/writing/style/#we-are-all-responsible-users)，不可能创建实际的*对象*，也不可能*变异*。然而，你可以创造出行为像不可能变异的物体或者不会被错误变异的真实物体。

让我们来看三种方法以及它们的区别。

## 命名元组

我目前从事的 Python 项目是在[数据类](https://docs.python.org/3/library/dataclasses.html)可用之前开始的。
此外，这个项目是为一个喜欢使用尽可能少的依赖的客户创建的。
在这种情况下，出现了以下积分类别:

```
from collections import namedtuple

class Point(namedtuple("_Point", ["x", "y"])):
    def scale(self, scale):
        return Point(self.x * scale, self.y * scale)

    def translate(self, dx, dy):
        return Point(self.x + dx, self.y + dy) 
```

它是二维空间中的点的类。
当你调用`scale`或`translate`方法时，会返回一个新的点。这个类的变体扩展了一个命名元组`_Point`，它由两个名为`x`和`y`的字段组成。

当你试图改变这个类的一个实例时，你会看到一个`AttributeError` :

```
>>> from collections import namedtuple
>>> Point = namedtuple("_Point", ["x", "y"])
>>> p = Point(1, 2)
>>> p.x
1
>>> p.x = 2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: can't set attribute 
```

对我来说这看起来很像不变性。
这种方法的一个缺点是`p`不是一个实际的对象。
这是一个元组。

```
>>> SomethingCompletelyDifferent = namedtuple("SomethingCompletelyDifferent", "a b")
>>> a = SomethingCompletelyDifferent(1, 2)
>>> p == a
True
>>> p == (1, 2)
True 
```

取决于你如何使用这个类的实例，这可能是一件大事。
文件为 [attrs](https://www.attrs.org/en/stable/index.html) 包列表[多了几个不利面](https://www.attrs.org/en/stable/why.html#namedtuples)。

## Attrs

如果你不介意依赖，你可以使用前面提到的 [attrs](https://www.attrs.org/en/stable/index.html) 包，这样做:

```
import attr

@attr.s(frozen=True)
class Point:
    x = attr.ib()
    y = attr.ib()

    def scale(self, scale):
        return Point(self.x * scale, self.y * scale)

    def translate(self, dx, dy):
        return Point(self.x + dx, self.y + dy) 
```

在这种情况下，装饰器`@attr.s(frozen=True)`规定`x`和`y`的值不能通过简单的赋值来改变。
这就像你期望的那样:

```
>>> import attr
>>> @attr.s(frozen=True)
... class Point:
...     x = attr.ib()
...     y = attr.ib()
...
>>> p = Point(1, 2)
>>> p.x
1
>>> p.x = 2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/Users/lucengelen/.local/share/virtualenvs/python-immutable-1HIt_5XS/lib/python3.7/site-packages/attr/_make.py", line 428, in _frozen_setattrs
    raise FrozenInstanceError()
attr.exceptions.FrozenInstanceError
>>> p == (1, 2)
False
>>> p == Point(1, 2)
True
>>> p == Point(2, 1)
False 
```

您仍然可以改变这个类的实例，但不是偶然的:

```
>>> p = Point(1, 2)
>>> p.__dict__["x"] = 100
>>> p
Point(x=100, y=2) 
```

## 数据类

从 Python 3.7 开始，你可以使用[数据类](https://docs.python.org/3/library/dataclasses.html)来实现类似于使用[属性](https://www.attrs.org/en/stable/index.html) :
的变体

```
from dataclasses import dataclass

@dataclass(frozen=True)
class Point:
    x: int
    y: int

    def scale(self, scale):
        return Point(self.x * scale, self.y * scale)

    def translate(self, dx, dy):
        return Point(self.x + dx, self.y + dy) 
```

这里，装饰器`@dataclass(frozen=True)`规定`x`和`y`的值不能通过简单的赋值来改变。
这也是你所期望的:

```
>>> from dataclasses import dataclass
>>> @dataclass(frozen=True)
... class Point:
...     x: int
...     y: int
...
>>> p = Point(1, 2)
>>> p.x = 100
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<string>", line 3, in __setattr__
dataclasses.FrozenInstanceError: cannot assign to field 'x'
>>> p = Point(1, 2)
>>> p == Point(1, 2)
True
>>> p == Point(2, 1)
False
>>> p == (1, 2)
False 
```

你可以用和上面一样的方法改变实例，但是我不会相信你说你做错了。

## 结论

如果您想尝试这些变体，可以使用 Python shell。
你也可以看看下面的回购:[https://github.com/ljpengelen/immutable-python-objects](https://github.com/ljpengelen/immutable-python-objects)。

在回顾了这些变体之后，我个人的结论是，我还不会替换现有项目中的所有命名元组。我不希望被有关平等的不幸行为所伤害。然而，对于未来的项目，我可能会选择数据类。
# Python 排序

> 原文：<https://dev.to/musale/python-tips-sorting-4of2>

# 简介

排序是日常编程中的一项常见功能。如果您使用 Python，您可能熟悉对列表值进行排序的两种常用方法。当`list.sort()`被构建到列表中时，`sorted()`函数是对任何 iterable 进行排序的常用方法。这两个函数的主要区别在于，`list.sort()`就地修改列表，而`sorted()`创建一个新的排序项目列表。

## 基本排序

```
>>> sorted([4, 1, 3, 5, 2, 5, 8])
[1, 2, 3, 4, 5, 5, 8]
>>> l = [8, 24, 63, 12, 5, 23, 2, 6, 7, 8]
>>> l.sort()
>>> l
[2, 5, 6, 7, 8, 8, 12, 23, 24, 63] 
```

Enter fullscreen mode Exit fullscreen mode

### 提示 1:逆序

这两种方法都允许一个可选的布尔值`reverse`键。您可以使用它将排序顺序从升序改为降序。

```
>>> sorted([4, 1, 3, 5, 2, 5, 8], reverse=True)
[8, 5, 5, 4, 3, 2, 1] 
```

Enter fullscreen mode Exit fullscreen mode

### 提示二:按键功能

这两个列表方法有一个`key`函数，它允许您传递一个在对每个值进行排序之前运行的函数。

例如，如果我想用大写字母对一个字符串排序，我会这样做:

```
>>> sorted("For instance, if I want to order a".split(), key=str.upper)
['a', 'For', 'I', 'if', 'instance,', 'order', 'to', 'want'] 
```

Enter fullscreen mode Exit fullscreen mode

在某些情况下，您可能有一个复杂对象的列表，您希望使用它们自己的索引对其进行排序。您可以传递引用索引的函数:

假设您有一个带有`Name`、`Size`和`Price`属性的手提包对象列表，您可以使用`Price`对它们进行排序，比如:

```
>>> handbags = [("Gucci", "L", 123), ("Vuitton", "M", 251), ("Jacobs", "S", 207)]
>>> handbags.sort(key=lambda bag: bag[2] )
>>> handbags
[('Gucci', 'L', 123), ('Jacobs', 'S', 207), ('Vuitton', 'M', 251)] 
```

Enter fullscreen mode Exit fullscreen mode

### 提示 3:多级排序

假设您想根据袋子的`Price`和`Size`对它们进行分类，您可以使用 Python 的[操作符](https://docs.python.org/3/library/operator.html#module-operator)模块来完成，该模块提供了`itemgetter`和`attrgetter`。

```
>>> from operator import itemgetter, attrgetter
>>> sorted(handbags, key=itemgetter(1,2))
[('Gucci', 'L', 123), ('Vuitton', 'M', 251), ('Jacobs', 'S', 207)] 
```

Enter fullscreen mode Exit fullscreen mode

或者创建一个带有命名属性的对象列表，并使用名称进行排序:

```
>>> class Bag:
...    def __init__(self, name, size, price):
...      self.name = name
...      self.price = price
...      self.size = size
...    def __repr__(self):
...      return repr((self.name, self.size, self.price))
...
>>> bags = [Bag("Gucci", "L", 123), Bag("Vuitton", "M", 251), Bag("Jacobs", "S", 207)]
>>> bags.sort(key=attrgetter("price", "size"))
>>> bags
[('Gucci', 'L', 123), ('Jacobs', 'S', 207), ('Vuitton', 'M', 251)] 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

在 Python 中对列表项进行排序是非常容易的，这些技巧可以用来以很小的编码复杂度实现更多的功能。如果你担心排序的稳定性，Python 排序保证是稳定的。这意味着当多个记录具有相同的键时，它们的原始顺序将被保留。
Python 使用 [Timsort](https://en.wikipedia.org/wiki/Timsort) 算法，该算法可以有效地进行多种排序，因为它可以利用数据集中已经存在的任何排序。你可以在这里阅读更多关于 Python 中排序的内容
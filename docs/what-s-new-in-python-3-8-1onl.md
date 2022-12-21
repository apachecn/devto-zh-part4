# Python 3.8 有什么新特性？

> 原文：<https://dev.to/deepsource/what-s-new-in-python-3-8-1onl>

最新最棒的 Python 版本将很快推出测试版。虽然在最终稳定版本推出之前还有一段时间，但所有的新功能都值得一试。Python 3.8 为该语言添加了一些新语法，对现有行为做了一些小的改变，主要是一些速度上的改进——保持了早期 3.7 版本的传统。

这篇文章概述了你应该知道的关于 Python 3.8 的最重要的增加和变化。看一看！

## 1。海象运营商

Python 中的赋值表达式带有“walrus”操作符`:=`。这将使您能够将值作为表达式的一部分赋给变量。这样做的主要好处是，当您想在后续条件中使用表达式的值时，可以节省一些代码行。

所以，大概是这样的:

```
length = len(my_list)
if length > 10:
    print(f"List is too long ({length} elements, expected <= 10)") 
```

现在可以简短地写成这样:

```
if (length := len(my_list)) > 10:
    print(f"List is too long ({length} elements, expected <= 10)") 
```

为简洁而欢呼，但有些人可能会说这会影响代码的可读性——可以说这里的第一个变体更清晰明了。这个讨论是 Python 社区中一个主要争议的中心。

## 2。仅位置参数

现在，在定义方法的参数时，可以使用一个特殊的标记符`/`，来指定函数只接受标记符左边的位置参数。Python 中只有关键字的参数在函数中使用了`*`标记，为只有位置的参数添加了`/`标记提高了语言的一致性，并支持健壮的 API 设计。

以这个函数为例:

```
def pow(x, y, z=None, /):
    r = x**y
    if z is not None:
        r %= z
    return r 
```

这里的`/`标记意味着传递`x`、`y`和`z`的值只能在位置上完成，不能使用关键字参数。行为如下图:

```
>>> pow(2, 10)  # valid >>> pow(2, 10, 17)  # valid >>> pow(x=2, y=10)  # invalid, will raise a TypeError >>> pow(2, 10, z=17)  # invalid, will raise a TypeError 
```

在 [PEP 570](https://www.python.org/dev/peps/pep-0570/) 中可以找到关于动机和用例的更详细的解释。

## 3。f 弦现在支持“=”

Python 程序员经常使用“printf 风格”的调试。在过去，这相当冗长:

```
print "foo=", foo, "bar=", bar 
```

f 弦让这个更好一点:

```
print(f"foo={foo} bar={bar}") 
```

但是你还是要重复自己:你要写出*字符串*“foo”，然后是*表达式*“foo”。

用作`f'{expr=}'`的`=`说明符扩展为表达式的文本，一个等号，然后是被求值的表达式的 repr。所以现在，你可以简单地写:

```
print(f"{foo=}  {bar=}") 
```

这是语言的一小步，但对于每个为调试而抛出`print()`语句的人来说却是一个巨大的飞跃！

## [4](#4-raw-reversed-endraw-now-works-with-raw-dict-endraw-)。`reversed()`现在与`dict`一起工作

从 Python 3.7 开始，字典保留了键的插入顺序。内置的`reversed()`现在可以用来以与插入相反的顺序访问字典——就像`OrderedDict`一样。

```
>>> my_dict = dict(a=1, b=2)
>>> list(reversed(my_dict))
['b', 'a']
>>> list(reversed(my_dict.items()))
[('b', 2), ('a', 1)] 
```

## 5。`return`和`yield`的简化可重复拆包

这种无意的行为从 Python 3.2 开始就存在了，Python 3.2 不允许在`return`和`yield`语句中没有括号的情况下解包 iterables。

因此，以下内容是允许的:

```
def foo():
    rest = (4, 5, 6)
    t = 1, 2, 3, *rest
    return t 
```

但是这些导致了`SyntaxError` :

```
def baz():
    rest = (4, 5, 6)
    return 1, 2, 3, *rest 
```

```
def baz():
    rest = (4, 5, 6)
    yield 1, 2, 3, *rest 
```

最新版本修复了这种行为，因此现在允许使用上述两种方法。

## 6。新语法警告

Python 解释器现在在某些情况下会抛出一个`SyntaxWarning`，当一个逗号在 tuple 或 list 前丢失时。所以当你不小心这样做的时候:

```
data = [
    (1, 2, 3)  # oops, missing comma!
    (4, 5, 6)
] 
```

不是显示`TypeError: 'tuple' object is not callable`，它并没有真正告诉你哪里出了问题，而是显示一个有用的警告，指出你可能漏掉了一个逗号。调试时非常有用！

当身份检查(`is`和`is not`)用于某些类型的文字(如字符串、整数等)时，编译器现在也会产生一个`SyntaxWarning`。).您很少想用除了`None`之外的文字来比较身份，编译器警告可以帮助避免一些难以捉摸的错误。

## 7。性能改进

这个版本在解释器上增加了许多性能提升，这与之前的 3.7 版本如出一辙。

*   `operator.itemgetter()`现在速度提高了 33%。这是通过优化参数处理和为单个非负整数索引到元组的常见情况(这是标准库中的典型用例)添加快速路径实现的。

*   `collections.namedtuple()`中的字段查找现在快了两倍多，这使得它们成为 Python 中最快的实例变量查找形式。

*   如果输入 iterable 的长度已知(输入实现 len)，那么`list`构造函数不会过度分配内部项缓冲区。这使得创建的列表平均缩小了 12%。

*   类变量写现在快了一倍:当一个非 dunder 属性被更新时，有一个不必要的调用来更新 slots，这是优化的。

*   一些简单的内置函数和方法的调用现在快了 20-50%。将参数转换为这些方法的开销减少了。

*   现在使用插槽来减少它的内存占用。

## 总结

即将发布的 Python 为该语言添加了一些很棒的新特性，并通过基本的加速修复显著提高了性能。在升级到 Python 3.8 时，有少量的[行为变化](https://docs.python.org/3.8/whatsnew/3.8.html#porting-to-python-3-8)可能需要修改现有代码，但是性能提升和新语法使这一努力完全值得。一个详细的变化日志的所有新的可以找到[在这里](https://docs.python.org/3.8/whatsnew/3.8.html)。

*最初发布于 [DeepSource 博客](https://deepsource.io/blog/python-3-8-whats-new/)T3。*
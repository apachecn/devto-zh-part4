# Python 中函数的布尔参数

> 原文：<https://dev.to/ivergara/boolean-arguments-to-functions-in-python-4dn8>

最近我读了 M. Fowler 写的下面这篇关于函数签名中布尔标志使用的短文。你看的时候我会等着的……看完了吗？很好！

一般来说，我完全同意他的意见。它们可能会令人困惑，通常应该避免(说起来容易做起来难)。但是这篇文章让我在 Python 的背景下思考这个建议。

本文中的例子将转换成 Python(带有类型注释和 PEP8 ),如下所示

```
class Concert:def book(self, customer: Customer, is_premium: bool):
    ... 
```

Enter fullscreen mode Exit fullscreen mode

这个定义确实有文章中提到的所有缺点。但是如果我们使用 Python 特有的`keyword argument`特性呢？

```
class Concert:def book(self, customer: Customer, *, is_premium: bool = False):
    ... 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，通过在签名中添加`*`,在调用函数时会被迫提到参数的名称。于是，一个类似下面的调用

```
a_book = concert.book(customer, True) 
```

Enter fullscreen mode Exit fullscreen mode

将引发一个`TypeError`异常。那么，调用函数的正确方式应该是

```
a_book = concert.book(customer, is_premium=True) 
```

Enter fullscreen mode Exit fullscreen mode

旗帜指向什么没有任何模糊之处，完全透明和明确。

然而，由于在大多数情况下布尔标志意味着函数代码中的一些分支，这种方法仍然有一些缺点，可以通过去掉这样的布尔标志来避免。

归根结底，这是一个判断的问题。如果这个函数基本上有一个功能非常有限的标志，它仍然是可管理的。
# Python 初学者的 5 个常见错误

> 原文：<https://dev.to/deepsource/5-common-beginner-mistakes-in-python-1j6h>

Python 作为一种高级动态编程语言，以易于使用而闻名。这使得它在 T2 广受欢迎，并在最近几年不断增长。易用性也带来了*误用*的便利。我们列出了初学者在用 Python 写代码时可能犯的 5 个常见错误。

## 1。不必要的 lambda 表达式

函数是 Python 中的一等公民。这意味着您可以将它赋给某个变量，在另一个函数调用中将它作为参数传递，等等。对于初学者或来自其他编程语言的开发人员来说，这可能是违反直觉的。

这种模式的一个常见例子是:

```
def request(self, method, **kwargs):
    # ...
    if method not in ("get", "post"):
        req.get_method = lambda: method.upper()
    # ... 
```

推荐的写法是:

```
def request(self, method, **kwargs):
    # ...
    if method not in ("get", "post"):
        req.get_method = method.upper
    # ... 
```

[(举例)](https://github.com/stripe/stripe-python/blob/625d3f6c012cd56d59acf82da5ca67d86a8453b3/stripe/http_client.py#L547-L548)

## [T2【2】。升起`NotImplemented`](#2-raising-raw-notimplemented-endraw-)

类似的命名会让开发人员感到困惑，这就是其中的一个例子。`NotImplementedError`是一个异常类，当需要派生类来重写方法时，应该引发该异常类。`NotImplemented`是一个常量，用于实现二元运算符。当你养`NotImplemented`的时候，会养一只`TypeError`。

不正确:

```
class SitesManager(object):
    def get_image_tracking_code(self):
        raise NotImplemented 
```

正确:

```
class SitesManager(object):
    def get_image_tracking_code(self):
        raise NotImplementedError 
```

[(举例)](https://github.com/search?l=Python&q=raise+NotImplemented+language%3APython&type=Code)

## 3。可变默认参数

当执行函数定义时，Python 中的默认参数被求值*一次*。这意味着当函数被定义时，表达式只被评估一次，并且*相同的值*被用于每个后续调用。因此，如果你正在修改可变的缺省参数——一个`list`，`dict`，等等。，它将被修改用于所有将来的呼叫。

不正确:

```
def add_item(new_item, items=[]):
    items.append(new_item) 
```

正确:

```
def add_item(new_item, items=None):
    if items is None:
        items = []
    items.append(new_item) 
```

[(举例)](https://github.com/keras-rl/keras-rl/blob/master/rl/agents/dqn.py#L167)

## 4。使用`assert`语句作为保护条件

因为`assert`提供了一种简单的方法来检查一些条件和失败的执行，所以开发人员经常使用它来检查有效性。但是当用`-O`(优化)标志调用 Python 解释器时，`assert`语句会从字节码中删除。因此，如果在生产代码中使用`assert`语句进行面向用户的验证，那么这个代码块根本不会被执行——这可能会打开一个安全漏洞。建议只在测试中使用`assert`语句。

不正确:

```
assert re.match(VALID_ADDRESS_REGEXP, email) is not None 
```

正确:

```
if not re.match(VALID_ADDRESS_REGEXP, email):
    raise AssertionError 
```

[(举例)](https://github.com/syrusakbary/validate_email/blob/master/validate_email.py#L127)

## 5。使用`isinstance`代替`type`

`type`或`isinstance`都可以用来检查 Python 中对象的类型。但是有一个重要的区别——`isinstance`在解析对象类型时会考虑继承，而`type`不会。所以有时候使用`isinstance`可能不是你想要的。看看下面的例子:

```
def which_number_type(num):
    if isinstance(num, int):
        print('Integer')
    else:
        raise TypeError('Not an integer')

which_number(False)  # prints 'Integer', which is incorrect 
```

这里，由于`bool`是 Python 中`int`的子类，`isinstance(num, int)`也被求值为`True`，这不是预期的行为。在这种特殊情况下，使用`type`是正确的方法。在这里阅读更多关于这两种方法行为差异的内容[。](https://stackoverflow.com/a/1549854/1088579)

*最初发表于 [DeepSource 博客](https://deepsource.io/blog/python-common-mistakes/)。准备好释放这些反模式的代码库了吗？DeepSource 标记了所有这些以及许多其他内容。[今天报名](https://deepsource.io/signup?ref=devto)。*
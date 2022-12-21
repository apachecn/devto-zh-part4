# 一个简短的例子，说明打字如何让你的生活更轻松

> 原文：<https://dev.to/_darrenburns/a-simple-example-of-how-types-can-make-your-life-easier-bfa>

下面是一个例子，涵盖了我在用动态编程语言编写代码时经常遇到的一些问题，以及如何使用类型注释(更具体地说，枚举)重写相同的代码使我的生活变得更容易。下面的代码片段是用 Python 写的，但是适用于任何支持枚举的语言，比如 TypeScript、Java 等。

* * *

假设您在一家在线书店工作，您的任务是编写一些代码来预测和存储用户的图书格式偏好。在您正在处理的代码库中，您找到了一个听起来很有用的方法:

```
def save_preferred_book_format(format):
    ... 
```

您想在代码中的某个地方使用这个方法，但是一眼看去，您不知道如何正确地调用它。它显然只有一个参数，`format`，但是没有指出什么是`format`*。它是一个字符串，一个数字，还是某种对象？*

 *您深入研究方法体，看到像这样的代码隐藏在它的中间，它检查参数是否有效。

```
if format not in VALID_BOOK_FORMATS:
    raise ValueError(f"format must be one of {VALID_BOOK_FORMATS}") 
```

您跳转到`VALID_BOOK_FORMATS`变量，看到它的定义如下:

```
VALID_BOOK_FORMATS = ["AUDIOBOOK", "EBOOK", "PAPERBACK", "HARDBACK"] 
```

假设这是一个字符串列表，并且我们的方法检查了这个列表中包含的参数`format`，我们可以推断当我们调用`save_preferred_book_format`时，我们必须传递给它一个字符串，如果它不是这个列表中定义的字符串之一，我们的程序将会崩溃！

如果我们没有读过这个方法的完整实现，我们就不会知道这些。该方法的签名没有包含足够的信息来安全地使用它。在这种情况下，最初的开发人员非常友好地包含了对列表`VALID_BOOK_FORMATS`的引用，因此我们能够使用我们的探测技能来解决如何调用该方法的问题。

这是用动态类型语言(如 JavaScript 和 Python)编写的代码库中的一种常见习惯，如果每次想要调用一个函数时都必须执行它，这很快就会变得令人厌烦。如果函数有多个参数，复杂性会大大增加理解它所需的时间。

这是有问题的。如果干净代码背后的一个关键思想是**可重用性**，为什么你要穿越这么多的障碍去理解*如何*重用一个函数呢？设计良好的代码可以让你花更多的时间调用函数，而花更少的时间去研究如何使用它们。

你不必阅读函数体来学习如何调用它。

上面的方法是如何避免这个问题的呢？

我们知道一本书的副本是一个`EBOOK`或一个`PAPERBACK`，但它不可能同时是两者。许多编程语言都提供了一种简便的建模方式:**枚举类型**(枚举)！

下面是我们如何使用枚举代替字符串列表重写`VALID_BOOK_FORMATS`:

```
from enum import Enum

class BookFormat(Enum):
    AUDIOBOOK = 1
    EBOOK = 2
    PAPERBACK = 3
    HARDBACK = 4 
```

如何使用枚举类似于如何与单选按钮交互。有一组已定义的选项，但您一次只能选择其中一个。

现在我们可以更新原始的方法签名来注释之前令人困惑的参数:

```
def save_preferred_book_format(format: BookFormat):
    ... 
```

通过添加这个类型注释，我们已经告诉代码的读者，`format`参数的类型是`BookFormat`，因此该方法期望 4 种可能的变化:`AUDIOBOOK`、`EBOOK`、`PAPERBACK`或`HARDBACK`。我们已经将可能的输入值集的大小从无穷大减少到了四个，您不再需要查看方法的签名来理解如何使用它。

```
save_preferred_book_format(BookFormat.EBOOK) 
```

不仅如此，如果我们向函数传递一个无效值，我们的静态分析器、编辑器或编译器(例如`tsc`、PyCharm、`mypy`)会在我们运行代码之前告诉我们。如果我们在调用方法时出现了拼写错误，我们也会在运行代码之前被告知！

枚举只是使用类型系统的一个好处。它们使代码更容易阅读，使您不必在代码库中跳来跳去寻找如何调用函数，并且您的代码变得更不容易出错。

这篇文章甚至没有详尽地探讨重构代码以使用类型的好处，但希望它给了一些读者思考的食粮。

* * *

### 关于 Python 3.8 的一点说明

Python 3.8 提供了“文字类型”，它让我们指定我们的代码必须用特定的文字来调用。我们可以重写我们的方法，通知我们代码的读者，它将只接受使用文字类型的特定字符串，如下所示:

```
_FormatType = Literal["AUDIOBOOK", "EBOOK", "PAPERBACK", "HARDBACK"]
...
def save_preferred_book_format(format: _FormatType):
    ... 
```

现在，如果我们用任何不包含在列表中的字符串调用我们的代码，我们会在运行它之前得到通知！

* * **
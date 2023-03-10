# Python 类型注释的失败实验

> 原文：<https://dev.to/mortoray/a-failed-experiment-with-python-type-annotations-2p40>

我喜欢 Python，但希望它有静态类型。增加的安全性将对提高质量和减少开发时间大有帮助。所以今天我尝试使用类型注释和一个名为 [mypy](http://mypy-lang.org/) 的静态类型检查器。

做了几个基础测试后，我很兴奋。但是我的喜悦很快变成了失望。有两个基本问题使它成为不可用的解决方案。

*   在类型注释中不能有自引用类，因此没有容器
*   不能有推断的返回类型值，因此需要大量浪费的注释

两个问题都来看。

## 自指

我正在为[的采访准备一些文章。代码](https://interview.codes)与我的 [MDL 处理器](https://github.com/mortoray/mdl/)。解析器使用一个`Node`类来创建一个解析树。这个类包含作为树结构一部分的`Node`子类。从逻辑上讲，这意味着我应该有如下的函数。

```
class Node(Object):

    def add_sub( self, sub : Node ):
        ...

    def get_subs( self ) -> Sequence[Node]:
        ... 
```

Enter fullscreen mode Exit fullscreen mode

理解这一点没有问题，但不幸的是这不是有效的 Python 代码。你不能在`Node`类中引用`Node`。

建议的解决方法是使用`TypeVar`。

```
NodeT = TypeVar( `NodeT`, bound=`Node` )
class Node(Object):
    def add_sub( self, sub : NodeT ):
        ...

    def get_subs( self ) -> Sequence[NodeT]:
        ... 
```

Enter fullscreen mode Exit fullscreen mode

这是丑陋的。我想起了 C++的`_t`模式。Python 吸引我的部分原因是它简化的语法。不得不像这样装饰班级使它远没有吸引力。另外，它是锅炉板代码，为以后的理解增加了开销。

Python 中的限制来自于`Node`还不在符号表中。直到类被处理后，它才进入符号表，这意味着你不能在类中使用`Node`。这是编译器的一个限制。没有理由一定要这样，除非是为了向后兼容古怪的旧代码。

也许我们不能使用类名。但是我们可以用一个`Self`或`Class`符号来表示封闭类。

## 没有推断的返回类型

Python 最大的价值之一是不必到处都放类型。你可以像下面这样写函数。

```
def get_value():
    return 123 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您使用 TypeScript 或 C++，编译器可以很高兴地推断出函数的返回类型。由于未知的原因，`mypy`选择不推断函数的返回类型。相反，如果没有类型注释，它假设返回类型`Any`。

这意味着我必须用静态类型检查器已经知道的信息来注释所有的函数。又多余又乱。

你还被迫学习所有类型的名称和结构。那些你可以安全忽略的问题。

```
def get_iter():
    return iter(sequence)

def get_closure(self):
    return lamba q : self.op(q) 
```

Enter fullscreen mode Exit fullscreen mode

为什么我必须知道`iter`返回的类型才能写这个函数？或者你知道什么类型的`get_closure`返回吗？我知道如何使用 return，甚至可以推断出它是一个函数，但是我不知道如何指定它的类型。知道无数的类型是不可行的。你将会花更多的时间去调整类型，而不是使用代码。

这种复杂性有助于将关键字`auto`引入 C++。在许多情况下，写入类型信息是不可行的。在处理参数容器类时尤其如此，

推断返回类型是一个基本特性。

## 暂时避而不谈

这两个问题在我的代码库中反复出现。当存在偶尔影响代码的限制时，我没问题，但这是基本的。要使用类型检查，我必须向每个类似容器的类添加多余的类声明。要使用类型检查，我必须注释所有函数的返回值。

静态类型检查不应该是一种折衷，没有什么根本的理由不能解除这些限制。当这些问题解决后，我会很高兴地回来使用类型注释。

* * *

*图片来源:[玛丽卡门](https://pixabay.com/users/marydesign-8283221/)T3】*
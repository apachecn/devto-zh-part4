# Python 中的函数装饰问题

> 原文：<https://dev.to/jctully/function-decorators-problem-in-python-34i2>

我最近遇到了一个有趣的问题，是关于一个我非常不熟悉的概念:函数装饰器。这个问题来自电子邮件订阅的日常编码问题。它指出:

> cons(a，b)构造一个对，car(pair)和 cdr(pair)返回该对的第一个和最后一个元素。例如，car(cons(3，4))返回 3，cdr(cons(3，4))返回 4。
> 
> 给定 cons 的这种实现:
> 
> ```
> def cons(a, b):
>     def pair(f):
>         return f(a, b)
>     return pair 
> ```
> 
> 实施 car 和 cdr。

有了一些关于 Lisp 语言(Scheme)的经验，我大概明白了被问到的问题:用 python 实现这些 Lisp 函数。但是给定代码中发生的事情对我来说很奇怪，实际上在这几行代码中发生了很多事情。我们在函数中有一个函数，closure 的内部函数访问 a 和 b，尽管它们没有在函数中定义，pair()接受一个函数作为参数，cons()接受一个函数作为返回值。还有，f 到底是个什么鬼？

看了一会儿这个问题后，我还是不明白。我想“cons 是一个函数，它返回一个函数，这个函数接受一个函数，并把它应用到 cons 的参数中”，这太难让我理解了。在查阅了一些关于函数装饰器的资料后，我有了一些更好的直觉，并意识到 cons 正在通过告诉 pair 将它的参数应用于什么来修改 pair。然后我重申了我的分析“pair 是一个问‘我应该对 a 和 b 做什么？’而 cons 是一个为某些 a 和 b 制造配对函数的函数”。这对我来说更有意义，我能够想出下面的解决方案:

```
def car (func):
    def retFirst(a,b):
        return a
    return func(retFirst) 
```

使用 lambda 函数可以进一步简化我的总体答案:

```
def car (f):
    return(f(lambda a,b: a))
def cdr (f):
    return(f(lambda a,b: b)) 
```

只是一些我偶然发现的有趣的东西，也许你也会发现！这也是我在网站上的第一篇文章。
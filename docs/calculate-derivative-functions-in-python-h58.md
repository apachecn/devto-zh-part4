# 导数 Python 在 Python 中计算导数函数

> 原文：<https://dev.to/erikwhiting88/calculate-derivative-functions-in-python-h58>

用 Python 可以做很多很酷的事情，今天我们要学习计算导数。

# 什么是导数

还记得微积分 1 吗？我也是，所以让我们快速复习一下。

导数是计算函数在给定点的变化率的方法。比如加速度是速度的导数。如果你有一个可以表示为`f(x) = 2x^2 + 3`的函数，那么这个函数的导数，或者这个函数变化的速率，可以用`f'(x) = 4x`来计算。

*注:如果你不知道，`f'(x)`读作“x 的 f 素数”*

导数在很多领域都有很多用途，但是如果你想知道如何用 Python 计算导数，你可能不需要我做更多的解释，所以让我们开始吧。

# 启动 Python 控制台

如果您还没有 SymPy 库，请继续运行`pip install sympy`。SymPy 是一个 Python 库，旨在成为一个成熟的计算机代数系统(CAS ),这本身就是一个非常酷的东西，但是让我们继续努力。首先，让我们设置好一切:

```
>>> from sympy import *
>>> # We have to create a "symbol" called x
>>> x = Symbol('x') 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经导入了库并创建了一个符号，让我们为`2x^2 + 3`创建函数(数学函数，不是 Python 函数):

```
>>> f = 2*x**2+3
>>> f_prime = f.diff(x) 
```

Enter fullscreen mode Exit fullscreen mode

这些看起来像什么？

```
>>> f
2*x**2+3
>>> f_prime
4*x 
```

Enter fullscreen mode Exit fullscreen mode

这就是真正的意义所在。但这些都不是实际的函数，所以它们对我有什么用，除非我只是想在作业上作弊？如果我正在写一个程序，通过寻找导数的麻烦，我可能打算把它作为一个函数，对吗？好吧，SymPy 用一个叫做`lambdify`的方便函数支持你，在这个函数中我们传递我们的符号和(数学)函数:

```
>>> f = lambdify(x, f)
>>> f_prime = lambdify(x, f_prime)
>>> # Let's test it out
>>> f(3)
21
>>> f_prime(3)
12 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道了。很简单，对吧？？

# 结论

如果您已经深入到本文中，并且您对自己说“我真的永远不会使用它”,请考虑一下:如果有一个 Python 库可以找到导数，然后将它们转换成函数，那么可能就有一个库可以满足您的任何需求。如果你发现自己将要承担一项非常复杂的任务，或者你有一个很好的应用程序的想法，但是害怕底层的实现太多而无法自己编写，那么快速地在 Google 上搜索一下，看看是否已经有了一些东西。

编码快乐！一如既往，如果你有任何问题，请告诉我。
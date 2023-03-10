# 递归 v 迭代函数

> 原文：<https://dev.to/ekand/recursive-v-iterative-function-51b4>

这是基于我在课程[计算机科学入门和在 Edx 上使用 Python](https://courses.edx.org/courses/course-v1:MITx+6.00.1x+2T2019/course/) 编程中学习的一课。这是关于迭代函数和递归函数的。
区别，就我的理解，是迭代反复执行几行代码，直到满足某个条件，而递归函数反复调用自己，直到满足某个条件。
在本例中，我将使用其中的一个来计算一个简单的幂函数(结果= base^ex)，但这些函数将通过基数乘以自身的指数来计算结果，而不是使用 Python 的内置指数函数(**)。
我承认，这是多余的，也许不是很有用，但这是一个学习练习。

我将从迭代的例子开始。先编码，然后我来做一些观察。

```
def iterPower(base, exp):
    '''
    base: int or float.
    exp: int >= 0

    returns: int or float, base^exp
    '''
    result = 1
    i=exp
    while i > 0:
        result = result * base
        i -= 1
    return result 
```

Enter fullscreen mode Exit fullscreen mode

我使用了一个 while 循环，条件是它在索引 I 大于零时执行。这个索引从指数的值开始，它等于 base 需要乘以自身的次数。每次循环执行时，exp 递减 1，变量 result 乘以基数。我将 result 的起始值设置为 1 有两个原因。首先，如果 exp 输入等于 0，while 循环将不会执行，我们需要一种方法让函数返回正确的答案 1。此外，将 result 设置为 1 会初始化变量 result，以便它可以在 while 循环中乘以 base。
然后函数返回结果。

TLDR:while 循环允许基数乘以自身 exp 倍。

接下来的递归例子:

```
def recurPower(base, exp):

    '''
    base: int or float.
    exp: int >= 0

    returns: int or float, base^exp
    '''

    if exp == 0:
        return 1
    else:
        return base * recurPower(base,exp-1) 
```

Enter fullscreen mode Exit fullscreen mode

这一次，通过在自身内部调用 recurPower 函数来实现基数相乘的重复。每次程序被调用时，变量 exp 在被送入函数的下一个实例之前减 1。当 exp 等于零时，函数中的函数隧道到达其终点。
注意，如果输入 exp 等于零，函数只返回 1，根本不自己执行。
我认为这个版本读起来更清晰，但一开始有点难以理解。在头脑中跟踪一个函数的多个实例相互调用是很棘手的。

我希望你学到了一些东西。我写这封信的时候确实这么做了。

又及:我是这个社区的新成员，我很高兴这是一个让我放心去冒险的地方。写一个我刚刚开始学习的主题。写一些非常基本的例子。

P.P.S .这是我第一次在 markdown 发表文章，我很喜欢。这很容易，输出看起来非常漂亮。
# 具有 Python 递归和记忆的斐波那契数列

> 原文：<https://dev.to/wangonya/fibonacci-sequence-with-python-recursion-and-memoization-f7h>

斐波纳契数列是一个数字序列，任何数字，除了第一个和第二个，都是前两个的和。例如:

```
0, 1, 1, 2, 3, 5, 8, 13, 21... 
```

Enter fullscreen mode Exit fullscreen mode

> **趣闻**:11 月 23 日被庆祝为斐波那契日，因为当日期以 mm/dd 格式(11/23)书写时，日期中的数字形成了斐波那契数列:1，1，2，3。

我们可以用公式来表示:

```
fib(n) = fib(n-1)+fib(n-2) 
```

Enter fullscreen mode Exit fullscreen mode

有了这个公式，我们可以写一个简单的[递归函数](http://pages.cs.wisc.edu/~calvin/cs110/RECURSION.html)来求解`fib(n)`。

<small>**注意:只用这个来测试小数字，最好是 n < 10。稍后我会解释**</small>

```
def fib(n):
    if n < 2:  # base case
        return n
    return fib(n-1) + fib(n-2)

if __name__ == "__main__":
  print(fib(7)) 
```

Enter fullscreen mode Exit fullscreen mode

### 为什么我们需要一个基础案例？

将公式直接转换成
非常简单

```
def fib(n):
    return fib(n-1) + fib(n-2) 
```

Enter fullscreen mode Exit fullscreen mode

但问题是，当你运行它时，它抛出一个 [`RecursionError`](https://docs.python.org/3/library/exceptions.html#RecursionError) 。这仅仅意味着程序中可能有一个无限循环。递归函数中的基本情况告诉函数何时停止(以避免进入无限循环),并且通常是已知的或者不需要函数就可以容易地解决的事情。在我们这里的例子中，我们从定义中知道，序列中的任何数字，**除了第一个和第二个**，都是前两个的和。我们用它来形成我们的基础案例`if n < 2: return n`。

## 提高效率

还记得我跟你说过只测试`n`值小的程序吗？原因如下。

目前，在`return`语句中，对`fib()`的每次调用都会导致对`fib()`的两次调用。调用树呈指数增长。计算`fib(5)`需要 15 个调用，`fib(10)`需要 177 个调用，`fib(20)`需要 21891 个调用...你明白了。为了解决这个问题，我们可以使用[记忆化](https://www.ocf.berkeley.edu/~shidi/cs61a/wiki/Memoization)。

记忆有助于避免对先前已经计算过的相同值进行不必要的计算。这就像人类的记忆一样。你已经记住了`2 x 2`,不需要使用计算器就可以立即给出答案。`799 x 377`？你可能需要用计算器来计算。如果出于某种原因，你发现你经常被问到`799 x 377`，把它背下来就好了，这样你就不用每隔一段时间就去计算了。`799 x 377`的值会一直保持不变，所以你要做的就是计算一次，把值保存在你的“缓存”(内存)里，每次需要的时候再检索。

幸运的是，python 有一个[内置的装饰器](https://docs.python.org/3/library/functools.html#functools.lru_cache)可以做到这一点。

```
from functools import lru_cache

@lru_cache(maxsize=None)
def fib(n):
    if n < 2:  # base case
        return n
    return fib(n-1) + fib(n-2)

if __name__ == "__main__":
  print(fib(50)) 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以安全地使用大量数据进行测试。
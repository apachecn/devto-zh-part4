# Python 生成器简介

> 原文：<https://dev.to/dandyvica/a-gentle-introduction-to-python-generators-4g10>

当我第一次接触 Python 生成器时，我发现它们相当晦涩，不容易理解。我没有找到对他们的明确介绍，也可能是我没有搜太多。这就是为什么我写了这篇文章，直接进入那些巨蟒的要点。

## Python 生成器定义

Python 生成器是:

*   Python 函数或方法
*   它充当迭代器
*   它跟踪它何时被调用(有状态的)
*   并使用 *yield* 关键字将数据返回给调用者

## 一个简单的例子开始

考虑这个函数:

```
def generator1():
    yield 1
    yield 2
    yield 3 
```

Enter fullscreen mode Exit fullscreen mode

直接调用这个函数只是返回一个生成器对象:

```
>>> generator1()
<generator object generator1 at 0x7fac361d8bf8> 
```

Enter fullscreen mode Exit fullscreen mode

***iter*** 和 ***next()*** 方法被自动实现:

```
>>> gen1 = generator1()
>>> dir(gen1)
['__class__', '__del__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__lt__', '__name__', '__ne__', '__new__', '__next__', '__qualname__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'close', 'gi_code', 'gi_frame', 'gi_running', 'gi_yieldfrom', 'send', 'throw'] 
```

Enter fullscreen mode Exit fullscreen mode

并且可用:

```
>>> next(gen1)
1
>>> next(gen1)
2
>>> next(gen1)
3
>>> next(gen1)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
> 
```

Enter fullscreen mode Exit fullscreen mode

返回 *StopIteration* ,因为生成器函数已经被调用了 3 次，并且没有更多要生成的内容。

因为是可迭代的，所以可以使用内置函数直接调用，比如 *list()* :

```
>>> gen1 = generator1()
>>> list(gen1)
[1, 2, 3]
>>> list(gen1)
[] 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到，在第二次调用之后，生成器对象已经耗尽，并返回一个空列表。

同 *tuple()* 或 *set()* 内置函数:

```
>>> gen1 = generator1()
>>> tuple(gen1)
(1, 2, 3)
>>> gen1 = generator1()
>>> set(gen1)
{1, 2, 3} 
```

Enter fullscreen mode Exit fullscreen mode

当然,结构中的*在这里是可用的:* 

```
gen1 = generator1()

# this will print out 1,2,3 for i in gen1:
    print(i) 
```

Enter fullscreen mode Exit fullscreen mode

## 超越移动

上面的例子只是为了让你理解*产出*的机制。

我们可以超越，将参数传递给生成器函数:

```
# returns a Fibonacci number < fib_max def fibonacci1(fib_max: int) -> int:
    # initial values
    fib_n_2 = 0
    fib_n_1 = 1
    yield fib_n_2
    yield fib_n_1

    # now general case
    fib_n = fib_n_2 + fib_n_1
    while fib_n <= fib_max:
        yield fib_n
        fib_n_2 = fib_n_1
        fib_n_1 = fib_n
        fib_n = fib_n_2 + fib_n_1

# gives: [0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, 610, 987] print(list(fibonacci1(1000))) 
```

Enter fullscreen mode Exit fullscreen mode

注意这不是一个递归函数。它只是在*处停止，产生*种断点，这对内存和堆栈都是有效的。

很容易修改前面的函数来产生无限的值:

```
from itertools import islice

# returns an infinite sequence of Fibonacci numbers def fibonacci2() -> int:
    # initial values
    fib_n_2 = 0
    fib_n_1 = 1
    yield fib_n_2
    yield fib_n_1

    # now general case
    fib_n = fib_n_2 + fib_n_1
    while True:
        yield fib_n
        fib_n_2 = fib_n_1
        fib_n_1 = fib_n
        fib_n = fib_n_2 + fib_n_1

# gives: [0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, 610, 987] print(list(islice(fibonacci2(), 17))) 
```

Enter fullscreen mode Exit fullscreen mode

## Python 生成器表达式

生成器表达式类似于列表理解，至少与语法相比是这样。

它们被用来用一个简单的表达式而不是一个函数来创建生成器对象，但是它们不够灵活，功能也不够强大:

```
# first 100 squares squares_gen = (x*x for x in range(100))

# only created here squares = list(squares_gen) 
```

Enter fullscreen mode Exit fullscreen mode

它们被延迟评估，意味着只有在必要的时候才执行。

希望这有所帮助！
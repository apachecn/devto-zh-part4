# 带装饰的 Python 中的大 O

> 原文：<https://dev.to/serhatteker/big-o-in-python-with-decorator-572c>

我们有时——总是？，想知道我们的代码有多高效。所以神奇的符号出现了:

> 大 O 符号在计算机科学中用于描述算法的性能或
> 复杂性。实际上，大 O 符号是一个特殊的符号，告诉你一个算法有多快。当然，你会经常使用预定义的算法
> ——当你这样做的时候，理解它们有多快或多慢是至关重要的。

效率涵盖许多资源，包括:

*   CPU(时间)使用率
*   内存使用
*   磁盘使用
*   网络使用

所有这些都很重要，但我们将讨论 CPU 时间:时间复杂度或
CPU 使用率。我们可以这样评价:

```
#!/usr/bin/env python
# -*- coding: utf-8 -*- 
import datetime

def foo():
    print("foo bar")

start_time = datetime.datetime.now()
foo()
finish_time = datetime.datetime.now()
duration = finish_time - start_time

print(f"Execution time: {duration}") 
```

Enter fullscreen mode Exit fullscreen mode

有什么更好的方法吗？是的，用一个`decorator`:

## Python 中的 decorators 是什么？

Python 有一个有趣的特性，叫做 decorators，用于向现有代码添加功能。

这也被称为`metaprogramming`，因为程序的一部分试图在编译时修改程序的另一部分。

装饰者允许我们包装另一个函数，以便扩展包装函数的行为，而不用永久地修改它。

在 decorators 中，函数被作为另一个函数的参数，然后在包装函数中被调用。

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*- 
import time
import random
import functools

def time_decorator(func):
    """ Calculate the execution time of a method and return it back"""
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        duration = time.time() - start

        print(f"Duration of {func.__name__} function was {duration}.")
        return result
    return wrapper

# with a function @time_decorator
def sum_random_numbers(ran_num):
    """ Return the sum between 0 and ran_num"""
    total_sum = 0
    for num in range(0, ran_num):
        total_sum += num
    return total_sum

# with a class instance method class Klass:
    @time_decorator
    def sum_random_numbers(self, ran_num):
        """ Return the sum between 0 and ran_num"""
        total_sum = 0
        for num in range(0, ran_num):
            total_sum += num
        return total_sum

if __name__ == "__main__":
    # generate a random number between 1.000.000 and 2.000.000
    ran_num = random.randint(1_000_000, 2_000_000)
    print(f"random_number: {ran_num}")

    sum_random_numbers(ran_num=ran_num)
    Klass().sum_random_numbers(ran_num=ran_num) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们运行该代码，输出将类似于:

```
random_number: 1123388
Duration of sum_random_numbers function was 0.05038046836853027.
Duration of sum_random_numbers function was 0.04922914505004883. 
```

Enter fullscreen mode Exit fullscreen mode

所以我们可以使用这个`decorator`来轻松地评估我们代码中各处的效率。

* * *

### 附录:

关于 Python 中装饰器的更多细节:
[函数和方法的装饰器](https://www.python.org/dev/peps/pep-0318/)

我也非常鼓励使用 python 内置的 [`logging`](https://docs.python.org/3/library/logging.html) 方法来代替
`print`。

```
import logging

logger = logging.getLogger(__name__)

logger.info(f"Execution time: {self.func.__name__} function was {duration}") 
```

Enter fullscreen mode Exit fullscreen mode
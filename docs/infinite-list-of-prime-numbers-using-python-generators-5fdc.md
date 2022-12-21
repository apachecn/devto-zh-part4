# 使用 Python 生成器的素数无限列表

> 原文：<https://dev.to/alebian/infinite-list-of-prime-numbers-using-python-generators-5fdc>

有时，我们希望创建计算成本非常高的元素集合。第一种选择是创建一个列表，等到所有的元素都计算完之后再使用它。虽然这样做可行，但效率并不高。为了提高效率，现代语言提供了一种创建自定义迭代器的方法，这样每个元素只在需要时才被计算(这也称为惰性初始化)。此外，迭代器允许我们创建无限的集合！

在我看来，Python 有一种最简洁优雅的方法来声明迭代器:[生成器](https://wiki.python.org/moin/Generators)。

事不宜迟，让我们试着创建一个素数的无限列表。

我们首先需要的是检测一个数是否是质数的方法:

```
from math import sqrt

def is_prime(n):
    if (n <= 1):
        return False
    if (n == 2):
        return True
    if (n % 2 == 0):
        return False

    i = 3
    while i <= sqrt(n):
        if n % i == 0:
            return False
        i = i + 2

    return True 
```

现在，使用我们的`is_prime`函数我们可以做:

```
def prime_generator():
    n = 1
    while True:
        n += 1
        if is_prime(n):
            yield n 
```

就是这样！只需调用函数并从中获取元素:

```
generator = prime_generator()

for i in range(10):
    print(next(generator)) 
```

或者创建前 N 个素数的列表:

```
from itertools import islice

array = [x for x in islice(prime_generator(), 10)] 
```

如您所见，迭代器定义是所有语言中最短、最简单的一种。

[![Buy Me A Coffee](img/5a370298d6333d837f8a2300ee8ce599.png)](https://www.buymeacoffee.com/alebian)
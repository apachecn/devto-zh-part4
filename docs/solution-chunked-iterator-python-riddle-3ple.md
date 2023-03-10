# 解决方案:分块迭代器、Python 谜语

> 原文：<https://dev.to/orenovadia/solution-chunked-iterator-python-riddle-3ple>

这是这个谜语的详细解答。

又来了:写一个函数(`chunks`)，输入是迭代器。输出是一个大小为`n`的迭代器。

第一个挑战是原始迭代器的长度未知。让我们从使用`itertools.islice`创建`n`大小的迭代器开始，而不关心原始迭代器的长度:

```
import itertools
from typing import TypeVar, Iterator

T = TypeVar('T')

def chunks(iterator: Iterator[T], n: int) -> Iterator[Iterator[T]]:
    while True:
        yield itertools.islice(iterator, 0, n)  # yield `n` size chunk of the iterator 

for chunk in chunks(iter(range(10)), 3):
    print(f'A {type(chunk)} type chunk, contains items: {tuple(chunk)}') 
```

输出为:

```
A <class 'itertools.islice'> type chunk, contains items: (0, 1, 2)
A <class 'itertools.islice'> type chunk, contains items: (3, 4, 5)
A <class 'itertools.islice'> type chunk, contains items: (6, 7, 8)
A <class 'itertools.islice'> type chunk, contains items: (9,)
A <class 'itertools.islice'> type chunk, contains items: ()
A <class 'itertools.islice'> type chunk, contains items: ()
.... forever 
```

使用`itertools.islice`,我们成功地将原始迭代器分块，但是我们不知道它何时耗尽。因此，`chunks`是一个永不结束的生成器函数。

为了克服这个问题，我们需要从原始迭代器中去掉一项。然后，我们将使用`itertools.chain`创建一个包含这一项和`n-1`多项的块。

```
 def chunks(iterator: Iterator[T], n: int) -> Iterator[Iterator[T]]:
    for first in iterator: # take one item out (exits loop if `iterator` is empty)
        rest_of_chunk = itertools.islice(iterator, 0, n - 1)
        yield itertools.chain([first], rest_of_chunk)  # concatenate the first item back 
```

输出为:

```
A <class 'itertools.chain'> type chunk, contains items: (0, 1, 2)
A <class 'itertools.chain'> type chunk, contains items: (3, 4, 5)
A <class 'itertools.chain'> type chunk, contains items: (6, 7, 8)
A <class 'itertools.chain'> type chunk, contains items: (9,) 
```

就是这样！

在理解的形式中(我通常更喜欢理解，但我必须承认这种形式不如循环形式易读):

```
 return (itertools.chain([first], itertools.islice(iterator, 0, n - 1))
            for first in iterator) 
```

### 表现

`islice` -ing 和`chain` -ing 的运行时开销远小于为`chunks`编写自己的定制代码，因为这两个功能都是在`C`(用于`CPython`运行时)中实现的。

### 告诫

这里有一个警告:整个解决方案假设`chunks`的消费者正在完全有序地使用迭代器。如果不是这样，由于`chunks`的懒惰，我们的块中的条目顺序可能与原始迭代器不一致。

示例:

```
chunk_iterator = chunks(iter(range(10)), 3)
first_chunk = next(chunk_iterator)
second_chunk = next(chunk_iterator)
print(tuple(second_chunk))  # (1, 2, 3) print(tuple(first_chunk))  # (0, 4, 5) 
```

干杯！
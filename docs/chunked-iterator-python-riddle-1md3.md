# 分块迭代器:Python 谜语

> 原文：<https://dev.to/orenovadia/chunked-iterator-python-riddle-1md3>

我真的很喜欢这个蟒蛇谜语。这(几乎)是一个实际问题。

假设你有一个迭代器(未知长度的对象流)。你想把它分成大小相等的块。例如:将一个文件分割成多个较小的文件。

对列表做同样的事情要容易得多，因为列表的长度是已知的:

```
from typing import List, TypeVar

T = TypeVar('T')

def chunks(l: List[T], n: int) -> List[List[T]]:
    return [l[i:i + n]
            for i in range(0, len(l), n)]

items = list(range(10))
print(chunks(items, 3))
# [[0, 1, 2], [3, 4, 5], [6, 7, 8], [9]] print(chunks(items, 4))
# [[0, 1, 2, 3], [4, 5, 6, 7], [8, 9]] 
```

谜语是:在输入是迭代器的地方写`chunks`。并且输出是迭代器的迭代器

```
 def chunks(l: Iterator[T], n: int) -> Iterator[Iterator[T]]:
    ???? 
```

我将在今天晚些时候或明天添加解决方案。

运行时性能的额外加分

后续问题:在这个问题的解决方案中，你能发现什么问题？

享受

编辑:解决方案是[这里的](https://dev.to/orenovadia/solution-chunked-iterator-python-riddle-3ple)
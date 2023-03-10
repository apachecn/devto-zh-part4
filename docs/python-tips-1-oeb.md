# Python 技巧# 1

> 原文：<https://dev.to/berolattidiego/python-tips-1-oeb>

这些技巧完全基于 Python 官方教程指南。

*   **在`for`语句中使用`else`:**

如果想在循环结束时立即触发一个动作，可以在`for`循环的末尾使用`else`子句。不会被`break` :
触发

```
 for num in range(2, 10):
    for x in range(2, n):
      if n % x == 0:
        print(n, 'equals', x, '*', n//x)
            break
      else:
        # loop fell through without finding a factor
        print(n, 'is a prime number') 
```

[来源](https://docs.python.org/3/tutorial/controlflow.html#if-statements)

*   **使用简单拷贝 vs 浅层拷贝 vs 深层拷贝:**

    *   简单复制或`=`只会创建一个对象的引用:

```
 old_list = [[1, 1, 1], [2, 2, 2], [3, 3, 3]]
  new_list = old_list
  new_list[2][2] = 9

  print('old_list:', old_list)
  print('id of old_list:', id(old_list))

  print('new_list:', new_list)
  print('id of new_list:', id(new_list)) 
```

```
 Old List: [[1, 1, 1], [2, 2, 2], [3, 3, 9]]
  ID of Old List: 140673303268168

  New List: [[1, 1, 1], [2, 2, 2], [3, 3, 9]]
  ID of New List: 140673303268168 
```

*   浅拷贝或`object[:]`将创建一个新对象，但将引用嵌套对象:

    ```
    import copy

    old_list = [[1, 1, 1], [2, 2, 2], [3, 3, 3]]
    new_list = old_list[:] # or copy.copy(old_list) old_list[1][1] = 'AA'

    print('old_list:', old_list)
    print('id of old_list:', id(old_list))

    print('new_list:', new_list)
    print('id of new_list:', id(new_list)) 
    ```

```
```
old_list: [[1, 1, 1], [2, 'AA', 2], [3, 3, 3]]
id of old_list: 140673303268168

new_list: [[1, 1, 1], [2, 'AA', 2], [3, 3, 3]]
id of new_list: 140673303268169
``` 
```

*   最后，深层拷贝将创建一个新对象，并将为内容创建新对象:

    ```
    import copy

    old_list = [[1, 1, 1], [2, 2, 2], [3, 3, 3]]
    new_list = copy.deepcopy(old_list)
    old_list[1][1] = 'AA'

    print('old_list:', old_list)
    print('id of old_list:', id(old_list))

    print('new_list:', new_list)
    print('id of new_list:', id(new_list)) 
    ```

```
```
old_list: [[1, 1, 1], [2, 'AA', 2], [3, 3, 3]]
id of old_list: 140673303268168

new_list: [[1, 1, 1], [2, 2, 2], [3, 3, 3]]
id of new_list: 140673303268169
``` 
```

```
[Source](https://www.programiz.com/python-programming/shallow-deep-copy) 
```

*   **函数中的默认值只计算一次:**

```
def f(a, L=[]):
    L.append(a)
    return L

print(f(1))
print(f(2))
print(f(3)) 
```

```
[1]
[1, 2]
[1, 2, 3] 
```

如果不希望在后续调用之间共享默认值，可以改为这样编写函数:

```
def f(a, L=None):
    if L is None:
        L = []
    L.append(a)
    return L 
```

[来源](https://docs.python.org/3/tutorial/controlflow.html#if-statements)

*   **使用`deque`实现双端队列:**

Deques 是栈和队列的概括(名字读作“deck”，是“double-end queue”的简称)。Deques 支持线程安全、内存高效的追加，并从 deques 的任一侧弹出，在两个方向上具有大致相同的 O(1)性能”。

```
 from collections import deque
  d = deque('ghi')
  d.append('j')
  d.appendleft('f')
  j = d.pop()
  f = d.popleft()
  for elem in d:
    print(elem.upper()) 
```

[来源](https://docs.python.org/3/tutorial/datastructures.html#using-lists-as-queues)
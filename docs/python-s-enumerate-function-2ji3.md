# Python 的枚举函数

> 原文：<https://dev.to/micahsgilbert/python-s-enumerate-function-2ji3>

这只是一个快速的帖子，因为我看到一些 python 初学者制作了一些真正需要`enumerate`的函数，但他们不知道它并转向奇怪的解决方案。

## 它有什么作用？

简而言之，`enumerate`是一个函数，它接受一个 iterable(通常是一个数组),并为每个项给出它在数组中的索引和该项本身。

## 什么时候该用？

我看过这个:

```
array = ["a","b","c","b"]
for index in range(len(array)):
    item = array[i]
    # code continues using both `index` and `item` 
```

这真的不会太疼，但是`enumerate`可以让它干净一点。

在我使用 Python 的早期，我创建了一个这样的怪物:

```
array = ["a","b","c","b"]
for item in array:
    index = array.index(item)
    # code continues using both `index` and `item` 
```

这是非常糟糕的代码，我的想法“一定有更好的方法来做这件事”最终将我引向了`enumerate`。
如果数组很长，除了可能的性能问题之外，这样做的主要问题是`array.index`只返回项目的第*个*索引。这意味着数组中的第二个`"b"`永远不会在剩余的`for`循环中运行它的索引(3)。相反，`array.index`将为两个`"b"`返回 1

## 应该怎么用？

难以置信的简单。只需要:

```
array = ["a","b","c","b"]
for index, item in enumerate(array)
    # code continues using both `index` and `item` 
```

看看这有多干净。这比上面的两个代码块使用的代码行都少，并且可以说是最显式和最容易理解的。

列表理解也可以用于过滤列表。

假设我们想要所有奇数索引的元音项。

```
test_letters = ["a", "e", "i", "q", "c"]
vowels = ('a','e','i','o','u')

# Not using `enumerate`, just plain old logic
# This ca be confusing, especially if it wasn't documented.

new_arr = []
for i in range(len(test_letters)):
    if i % 2 == 0 and test_letters[i] in vowels:
        new_arr.append(test_letters[i])

# With enumerate
# It's only one line and still makes a ton of sense.

new_arr = [letter for index,letter in test_letters if index % 2 == 0 and letter in enumerate(vowels)] 
```

是一个非常有用的函数，任何学习 Python 的人都应该使用它。
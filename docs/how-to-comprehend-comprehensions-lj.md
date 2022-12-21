# 如何理解理解

> 原文：<https://dev.to/austinbh/how-to-comprehend-comprehensions-lj>

对我来说，学习 Python 是一种非常有趣的语言。到目前为止，我最喜欢学习和使用的东西是理解。当我第一次看它们时，它们对我来说没有什么意义，看起来很奇怪。我不能说我现在是专家了，但是与几周前相比，我对他们现在做什么有了更好的了解。

希望我们都能更好地理解什么是理解，以及在这一课结束时它们能做什么。

## 什么是领悟？

这不是一个关于语法看起来像什么的问题(我们将会谈到)，而是一个关于理解是什么的问题。我发现极客的定义非常好:`Comprehensions in Python provide us with a short and concise way to construct new sequences (such as lists, set, dictionary etc.) using sequences which have been already defined.`

现在让我们把它分解一下，这意味着我们可以用已经存在的序列快速构建新的序列。这对我来说听起来还是有点费解，让我们这么说吧，理解让我们用旧序列快速创建新序列。

## 他们长什么样？

现在我们对理解有了一点概念，让我们看看一个简单的理解是什么样子的。

```
foo = [1, 2]
bar = [x for x in foo]
print(bar)
# [1, 2] 
```

这并不太糟糕，它看起来有点类似于列表中的一行。从语法上来说，即使我们后来添加了更复杂的理解，它仍然停留在这个领域。

例如，如果我们真的想改变我们理解中的原始数据，我们可以执行我们希望的任何操作。

```
foo = {'one': 1, 'two': 2}
bar = {k: v*2 for (k,v) in foo.items()}
print(bar)
# {'one': 2, 'two': 4} 
```

在这里，我们不得不做一些不同的事情。因为我们需要访问键和值，所以我们需要调用我们的`foo`字典上的`items`方法，这样我们就可以访问键和值。我们可以像上面的列表理解一样访问字典的键，但是要同时拥有这两个值，我们需要`items`方法。

```
foo = {'one': 1, 'two': 2}
bar = {item for item in foo}
print(bar)
print(type(bar))

# {'one', 'two'}
# <class 'set'> 
```

等一下，这不是一本字典，这是一套。我们一会儿将进入集合理解，但是现在只要记住，如果我们只访问我们的字典的键，我们将得到一个集合而不是字典作为回报。

## 为什么理解很重要？

首先，理解允许我们在不改变数据的情况下操作数据。我们既能获得新的字典、列表、集合或生成器，又能保留原始数据。当我们既需要更改原始数据，又需要根据原始数据返回新数据时，这非常有用。

例如，假设我们有一个包含前 5 个素数的列表。我们想把这 5 个质数相乘，但是我们仍然需要原始列表来进行比较。

```
foo = [2, 3, 5, 7, 11]
bar = [x**2 for x in foo]
print(foo)
print(bar)

# [2, 3, 5, 7, 11]
# [4, 9, 25, 49, 121] 
```

理解的另一个好处是，它允许我们使用比使用标准 for 循环更少的语法进行迭代。让我们看一下前面的例子，但是有一个 for 循环。

```
foo = [2, 3, 5, 7, 11]
bar = []
for num in foo:
    bar.append(num**2)
print(bar)

# [4, 9, 25, 49, 121] 
```

我们必须在声明列表的地方多写两行，然后实际上把每个数字附加到列表上。从这个意义上说，我们的理解不仅更短，而且一旦你看到了几个理解，就很容易理解。

## 再举几个例子

我只是想提供几个集合和生成器理解的例子，因为我们以前没有真正接触过它们。集合和生成器综合使用与字典和列表综合非常相似的语法。

```
# Dictionary 
foo = {'one': 1, 'two': 2}
bar = {k: v+5 for (k,v) in foo.items()}
print(bar)

# {'one': 6, 'two': 7} 
# Set 
foo = {1, 2, 3}
bar = {num ** 3 for num in foo}
print(bar)

# {8, 1, 27} 
# List 
foo = [1, 2, 3, 4, 5, 6]
bar = [num + 3 for num in foo if num % 2 != 0]
print(bar)

# [4, 6, 8] 
# Generator 
foo = [1, 2, 3]
bar = (num for num in foo if num % 2 == 0)
for num in bar:
    print(num)

# 2 
```

首先，我们看到集合、列表和生成器理解之间的语法相似性。字典理解看起来有点不同(因为键值对)，但是`for`、`in`和`if`关键字的顺序都是一样的。

让我们花一点时间来讨论列表的语法和生成器理解。我添加了一个 if 语句，这是前面的例子中没有的。这样做的结果是，我们的新集合、字典、列表或生成器将只包含 if 语句评估为 true 的元素。

看看这在标准 for 循环中是什么样子，这只是一个普通的旧 if 语句。

```
foo = [1, 2, 3]
bar = []
for num in foo:
    if num % 2 == 0:
        bar.append(num + 2)
print(bar)

# [4] 
```

我们在这里所做的就是给所有的偶数加 2。同样，通过我们的理解，我们能够简化语法(我们也不必担心缩进错误！).

当我刚开始学习 Python 的时候，理解是最先抓住我眼球的东西，几个星期后，它们仍然让我非常感兴趣。我当然喜欢发现这些语法上的小甜点，并对它们有更多的了解。希望这能让你更容易理解，我知道我花了一段时间才完全理解。

## 参考文献

*   [https://wiki.python.org/moin/Generators](https://wiki.python.org/moin/Generators)
*   [https://docs.python.org/3/tutorial/datastructures.html](https://docs.python.org/3/tutorial/datastructures.html)
*   [https://www.geeksforgeeks.org/comprehensions-in-python/](https://www.geeksforgeeks.org/comprehensions-in-python/)
*   [https://www.w3schools.com/python/ref_dictionary_items.asp](https://www.w3schools.com/python/ref_dictionary_items.asp)
# ' Python '是' vs '== '

> 原文：<https://dev.to/wangonya/python-is-vs-28eb>

很多时候，当我在 Python 中做`if` s 时，我发现自己不知道是使用`is`还是`==`进行检查。

```
# do I do
if a is b:
    ...

# or
if a == b:
    ... 
```

Enter fullscreen mode Exit fullscreen mode

如果您是 Python 的新手，这可能会有点混乱，并且很容易假设这两者可以互换使用。那么，有什么区别呢？

## `is`

`is`操作符检查两个元素是否指向同一个对象。让我们启动一个 python 控制台来帮助说明这一点:

```
$ python3
Python 3.7.4
[Clang 10.0.1 (clang-1001.0.46.4)] on darwin
Type "help", "copyright", "credits" or "license" for more information.

>>> a = []
>>> b = []
>>> c = a

>>> a
[]
>>> b
[]
>>> c
[]
>>> 
```

Enter fullscreen mode Exit fullscreen mode

所以，我们声明了三个变量，并给它们赋值。`a`和`b`都是空单，`c = a`。我们可以看到这三个变量都包含一个空列表。用`is`来对比:

```
>>> a is b
False
>>> b is c
False
>>> a is c
True 
```

Enter fullscreen mode Exit fullscreen mode

尽管`a`和`b`看起来相同(因为它们都是空列表)，变量`a`和`b`并不指向同一个对象，因此`a is b`的计算结果为`False`。`b is c`也是如此。

相反，因为我们将变量`a`赋给了`c`，它们都指向同一个对象，因此`a is c`就是`True`。

## `==`

另一方面，检查两个元素是否包含相等的值。它们是否指向同一个对象在这里并不重要。

```
>>> a == b
True
>>> b == c
True
>>> a == c
True 
```

Enter fullscreen mode Exit fullscreen mode

所有使用`==`的检查评估为`True`，因为`a`、`b`和`c`的值都相等。如果引入了`d = [1, 2, 3]`，那么`a == d`、`b == d`和`c == d`都将是`False`，因为这些值不相等。

所以如果你想检查元素是否指向同一个对象，使用`is`。如果您只对值的相等感兴趣，请使用`==`。
# 如何在 Python 中格式化字符串:插值、连接等等

> 原文：<https://dev.to/renegadecoder94/how-to-format-a-string-in-python-interpolation-concatenation-and-more-20pf>

我已经有一段时间没有写这些“如何做”的文章了，但是我又回来了。这一次，我想谈谈使用插值和连接等技术的字符串格式化。换句话说，是时候最终学习如何在 Python 中格式化字符串了

## 问题介绍

无论我们是试图提示用户还是输出一条漂亮的错误消息，字符串格式化总是具有挑战性。毕竟，不同语言的语法是不同的，这就像学习一种元语言。例如，在 Java 和 C 语言中，字符串格式化依赖于对变量参数和格式说明符等概念的理解:

```
printf("Hi, %s", Jeremy); # Prints "Hi, Jeremy" 
```

Enter fullscreen mode Exit fullscreen mode

当然，随着我们引入不同的数据类型，字符串格式变得更加复杂。比如数字有自己的一套说明符:`%d`、`%f`等。而且，我们甚至可以根据填充和截断来指定数字的外观。

也就是说，我不知道你是来学习 C 语言中的字符串格式的，那么我们如何在 Python 中完成同样的事情呢？在本文中，我们将看看几种方法——有些很傻——只是为了说明有多少种方法可以解决这个问题。

首先，我们需要一个通用的例子，其中包含一些陷阱，比如混合数字和字符串。下面的代码片段将作为本文剩余部分的基础:

```
name = "Jeremy"
age = 25 
```

Enter fullscreen mode Exit fullscreen mode

使用这些变量，我们将想要构造下面的句子:

```
print("My name is Jeremy, and I am 25 years old.") 
```

Enter fullscreen mode Exit fullscreen mode

当然，你可以随意把名字和年龄换成你的名字和年龄！

## 方案

事实证明，格式化字符串有很多种方法。我们将从一些直接的方法开始，然后我们将进入一些优雅的解决方案。

### 使用串联格式化字符串

如果你和我一样，串联是你刚开始编码时学到的东西。因此，串联看起来像是字符串格式化的快捷方式:

```
print("My name is " + name + ", and I am " + age + " years old.") 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，*这样的解决方案行不通*。如果您试图运行这段代码，您将会得到一个类似如下的令人讨厌的错误:

> 回溯(最近一次调用 last):
> 
> 文件""，第 1 行，在
> 
> “我的名字是“+姓名+”，我是“+年龄+”岁。”
> 
> 类型错误:只能将 str(不是“int”)连接到 str

希望`TypeError`给你提示，当我们试图将一个字符串和一个整数连接起来时，解释器不喜欢它。换句话说，我们需要将`age`变量转换成一个字符串:

```
print("My name is " + name + ", and I am " + str(age) + " years old.") 
```

Enter fullscreen mode Exit fullscreen mode

而且，就是这样！对于小字符串，这可能是好的，但它不是超级可读的。此外，很容易忘记我们要连接的变量两边的空格。幸运的是，还有其他方法来构建字符串。

### 使用多个打印语句格式化一个字符串

当我们可以多次调用 print 时，谁还需要连接呢？

```
print("My name is ", end="")
print(name, end="")
print(", and I am ", end="")
print(age, end="")
print(" years old.") 
```

Enter fullscreen mode Exit fullscreen mode

现在，我知道你在想什么；是的，这只在 Python 3+中有效。哦，这是一个完全荒谬的解决方案，但它展示了一些重要的东西:有很多方法可以解决同一个问题。

在这种情况下，我们采用了`print`函数，并利用它的一个默认参数(`end`)来移除换行行为。那样的话，我们可以把一些文本串在一起，而不需要连接。

同样，这肯定很难阅读，我甚至不建议它用于小字符串。也就是说，它确实消除了类型转换。不幸的是，它引入了许多重复的代码。

### 使用 Join 函数格式化字符串

继续我们对最荒谬的字符串格式化方式的探索，我带来了`join`函数。如果您不熟悉这个函数，它基本上是连接字符串的一种更有效的方法。此外，它允许我们提供一个分隔符来放置在连接的字符串之间。当然，我们不需要这个:

```
print(''.join(["My name is ", name, ", and I am ", str(age), " years old"])) 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们在一个空的分隔符字符串上调用了`join`方法。作为一个参数，我们传递给它一个字符串列表。当然，join 会将这个字符串列表组合成一个没有分隔符的字符串。

奇怪的是，我有点喜欢这个解决方案，因为它的可读性非常好。不幸的是，有几个缺点。例如，我们必须手动将所有变量转换成字符串。另外，这条线已经相当长了。不过，我想我们可以把所有的东西都放到它自己的线上。

无论如何，有了这三个障碍，我们终于可以开始得到一些更合理的解决方案。

### 使用%运算符格式化字符串

现在，我们开始研究实际的字符串格式化技术。事实证明，Python 有自己的一套格式化工具，类似于 C:
中的`printf`

```
print("My name is %s, and I am %d years old." % (name, age)) 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们构建了一个新的字符串，用`name`代替了`%s`，用年龄代替了`%d`。

除了知道[格式说明符](https://docs.python.org/3/library/stdtypes.html#printf-style-string-formatting)，我们还想学习语法。特别是，我们的模板字符串后面是模操作符。当然，在这种情况下，我们可以称之为字符串格式化或**插值**操作符。

然后，我们创建一个值的元组，我们想把它放在我们的字符串中。请务必确保这些值的顺序。如果顺序不对，结果字符串可能不正确，或者程序可能完全崩溃。

用这种方法，我们得到一个更干净的解决方案。当然，这里有陷阱，但它们主要与值如何映射到字符串有关。例如，我们必须注意如何对参数排序，我们需要知道我们的格式说明符。

说到格式说明符，如果我们想直接打印出一个对象呢？幸运的是，我们有更好的解决方案。

### 使用 Format 函数格式化字符串

除了使用花哨的重载操作符，我们还可以通过对字符串使用`format`函数来使我们的代码更具可读性

```
print("My name is {}, and I am {} years old".format(name, age)) 
```

Enter fullscreen mode Exit fullscreen mode

以前，我们必须使用格式说明符来获得我们想要的行为，但是现在我们可以只使用大括号。换句话说，我们已经从以前的解决方案中消除了一个问题。

据我所知，这个方法利用了对象的`__format__`方法，所以我们可以毫无问题地将任何东西传递给这个方法。还有一个问题！当然，如果这个类没有覆盖`__str__`或`__repr__`，那么这个对象就不会打印得很好。也就是说，我仍然认为这是对以前解决方案的一个胜利。

事实证明，我们也可以消除前面解决方案中的订购问题。我们所要做的就是提供关键字参数:

```
print("My name is {n}, and I am {a} years old".format(a=age, n=name)) 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们将年龄关键字命名为`a`，将姓名关键字命名为`n`。这样，我们可以将关键字放在它们各自的括号内。为了进一步证明这一点，我们甚至可以对论点进行重新排序。这真是太酷了！

当然，我应该警告你，这个解决方案[可能会对你的应用程序构成安全威胁](http://lucumr.pocoo.org/2016/12/29/careful-with-str-format/)，这取决于你如何使用它。如果你正在写你自己的格式字符串，不应该有任何问题。但是，如果您接受来自用户的格式字符串，您可能要小心。

### 用 f-Strings 格式化一个字符串

另一种执行字符串插值的方法是使用 Python 最新的 f-String 特性(Python 3.6+)。有了这个特性，我们需要做的就是在字符串前面加上字母`f`，然后像以前一样插入括号。但是，这一次，我们可以直接插入变量的名称:

```
print(f"My name is {name}, and I am {age} years old") 
```

Enter fullscreen mode Exit fullscreen mode

现在，这真是太优雅了。我们再也不用担心:

*   将参数映射到格式说明符
*   正确使用格式说明符
*   记住晦涩的语法

相反，我们预先考虑和`f`并插入我们的变量。就是这样！现在，我不知道这个解决方案是否有任何安全漏洞，但据我所知，没有办法将`f`应用于输入字符串。

无论如何，这就是我对字符串格式的所有解决方案。现在，让我们开始比较这些解决方案的性能。

## 表现

和往常一样，我喜欢先在字符串中设置我们所有的解决方案:

```
setup = """
name = "Jeremy"
age = 25
"""

concatenation = """
"My name is " + name + ", and I am " + str(age) + " years old."
"""

string_join = """
''.join(["My name is ", name, ", and I am ", str(age), " years old"])
"""

modulus = """
"My name is %s, and I am %d years old." % (name, age)
"""

format_ordered = """
"My name is {}, and I am {} years old".format(name, age)
"""

format_named = """
"My name is {n}, and I am {a} years old".format(a=age, n=name)
"""

f_string = """
f"My name is {name}, and I am {age} years old"
""" 
```

Enter fullscreen mode Exit fullscreen mode

为了我的理智，我必须删除打印声明。因此，我无法测试`print`解决方案。也就是说，请随意尝试。我遇到了一些输出字符串降低测试速度的问题，我甚至尝试重新路由`stdout`来处理它。至少可以说这是一场噩梦。

无论如何，现在只需要调用我们的`timeit`命令:

```
>>> import timeit
>>> min(timeit.repeat(stmt=concatenation, setup=setup, repeat=10))
0.4947876000000022
>>> min(timeit.repeat(stmt=string_join, setup=setup, repeat=10))
0.37328679999995984
>>> min(timeit.repeat(stmt=modulus, setup=setup, repeat=10))
0.29478180000000265
>>> min(timeit.repeat(stmt=format_ordered, setup=setup, repeat=10))
0.40419490000000735
>>> min(timeit.repeat(stmt=format_named, setup=setup, repeat=10))
0.49794210000000305
>>> min(timeit.repeat(stmt=f_string, setup=setup, repeat=10))
0.1918610999999828 
```

Enter fullscreen mode Exit fullscreen mode

正如 Python 中这些新特性的常见情况一样，它们得到了令人难以置信的优化。事实上，唯一能与 f 弦解决方案相媲美的解决方案是模数运算符解决方案。

此外，我认为值得注意的是，当参数被命名而不是被排序时,`format`函数会慢多少。事实上，它的速度和我预想的惊人的连接速度一样慢。毕竟，字符串是不可变的，所以串联应该很糟糕。

像往常一样，对这些性能指标有所保留。

## 稍微重述一下

综上所述，这里是所有解决方案的统一位置:

```
name = Jeremy
age = 25

# String formatting using concatenation print("My name is " + name + ", and I am " + str(age) + " years old.")

# String formatting using multiple prints print("My name is ", end="")
print(name, end="")
print(", and I am ", end="")
print(age, end="")
print(" years old.")

# String formatting using join print(''.join(["My name is ", name, ", and I am ", str(age), " years old"]))

# String formatting using modulus operator print("My name is %s, and I am %d years old." % (name, age))

# String formatting using format function with ordered parameters print("My name is {}, and I am {} years old".format(name, age))

# String formatting using format function with named parameters print("My name is {n}, and I am {a} years old".format(a=age, n=name))

# String formatting using f-Strings (Python 3.6+) print(f"My name is {name}, and I am {age} years old") 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们结束了。如果你喜欢这篇文章，并希望更多这样的内容进入你的收件箱，[订阅我们的时事通讯](https://newsletter.therenegadecoder.com/)。更好的是，[成为 Renegade Coder 社区](https://www.patreon.com/TheRenegadeCoder)的一员，并获得酷酷的奖励，比如像这篇文章结尾的署名。

一如既往，我尽量挑选我认为会给你带来一些价值的相关产品。如果你有任何你自己的产品想让我分享，请在下面的评论里写下来。

与此同时，为什么不通过浏览以下 Python 文章来改进一下我的站点指标呢:

*   [那次我发布了不安全的代码](https://therenegadecoder.com/blog/that-time-i-shipped-insecure-code/)
*   [如何自动化你的 GitHub Wiki](https://therenegadecoder.com/code/how-to-automate-your-github-wiki/)
*   [如何在 Python 中克隆列表:切片、复制等等](https://therenegadecoder.com/code/how-to-clone-a-list-in-python/)

再次感谢你们的支持，特别感谢我所有的赞助人让这一切成为可能。下次见！

帖子[如何在 Python 中格式化一个字符串:插值、连接等等](https://therenegadecoder.com/code/how-to-format-a-string-in-python/)首先出现在[这个叛逆的编码者](https://therenegadecoder.com)上。
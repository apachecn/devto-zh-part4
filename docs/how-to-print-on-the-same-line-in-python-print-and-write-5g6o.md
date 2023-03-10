# 如何在 Python 中打印同一行:打印和写入

> 原文：<https://dev.to/renegadecoder94/how-to-print-on-the-same-line-in-python-print-and-write-5g6o>

作为一个教授大量初学者编程内容的人，我偶尔会遇到类似“如何在 Python 中的同一行上打印”这样的问题幸运的是，我有一个答案！

**简而言之，在 Python 中有两种主要的在同一行上打印的方法。对于 Python 2，使用下面的打印语法:`print "Williamson",`。对于 Python 3，使用下面的打印语法:`print("Providence", end="")`。否则，请查看本文的剩余部分，了解向后兼容的解决方案。**

## 问题介绍

在许多编程语言中，在同一行打印通常是默认行为。例如，Java 有两个命令行打印函数:

```
System.out.print();
System.out.println(); 
```

Enter fullscreen mode Exit fullscreen mode

正如您可能想象的那样，Java 中默认的`print`函数将在没有换行符的情况下打印。相比之下，`println`函数的行为很像 Python 中的`print`函数。具体来说，它将打印您提供给它的任何字符串，后跟一个换行符(即`\n`)。

当然，如果 Python 中的`print`函数在每次调用时自动打印一个换行符，那么就没有办法得到 Java `print`的行为，对吗？幸运的是，这不是真的！否则，我就没什么可写的了。

## 方案

为了在 Python 中在同一行上打印，有几个解决方案。不幸的是，并非所有的解决方案都适用于所有版本的 Python，所以我提供了三个解决方案:一个适用于 Python 2，另一个适用于 Python 3，最后一个适用于两者。

### 打印在同一行的旧方式

当我在寻找这个问题的解决方案时，我发现了很多关于 Python 2 的资料，这些资料很快就会被淘汰(我希望如此)。也就是说，我觉得这个解决方案对那些还在摇摆的人会有帮助。

无论如何，当你用 Python 2 打印一些东西时，语法和 Python 3 是一样的，但是你去掉了括号:

```
print "Live PD" 
```

Enter fullscreen mode Exit fullscreen mode

当然，在这两种情况下，默认行为都是用换行符打印。因此，我们需要添加一点巧妙的语法——逗号:

```
print "Live PD", 
```

Enter fullscreen mode Exit fullscreen mode

现在，打印函数应该排除换行符。然而，**这个解决方案会在字符串**的末尾增加一个额外的空格。此外，您可能会注意到，此解决方案不会立即打印。如果发生这种情况，您可以打电话给`sys.stdout.flush()`。

### 打印与写功能在同一行

幸运的是，我们可以使用`sys`库中的函数`write`来弥合 Python 2 和 3 之间的差距。这个函数就像`print`函数一样工作，但是没有隐式换行符:

```
import syssys.stdout.write("Breaking Bad") 
```

Enter fullscreen mode Exit fullscreen mode

同样，由于没有换行符，您可能需要刷新缓冲区来查看任何结果:

```
import sys
sys.stdout.write("Breaking Bad")
sys.stdout.flush() 
```

Enter fullscreen mode Exit fullscreen mode

无论是哪种情况，这个解决方案都可以在 Python 的两个版本中完成工作。

### 在同一行上打印新的方式

在 Python 3 中，`print`是一个标准函数。因此，它有额外的参数机会。特别是，有一个名为`end`的关键字参数，默认为某个换行符。你可以很容易地改变它如下:

```
print("Mob Psycho 100", end="") 
```

Enter fullscreen mode Exit fullscreen mode

而且，就是这样！字符串不是以换行符结尾，而是以空字符串结尾。当然，这个解决方案与前两个解决方案有相同的警告:您可能需要刷新缓冲区。

## 表现

一如既往，我喜欢从性能的角度来看所有的解决方案。首先，我通常将每个解决方案存储在一个字符串中。为了避免测试过程中过多的打印，我选择写空字符串:

```
setup="""
import sys
"""

write_solution = """
sys.stdout.write("")
"""

print_solution = """
print("", end="")
""" 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，我无法在我的系统上测试 Python 2 解决方案，所以请在评论中分享您的结果。无论如何，我喜欢使用`timeit`库来进行一个快速而肮脏的性能测试:

```
>>> import timeit
>>> min(timeit.repeat(stmt=write_solution, setup=setup, repeat=10))
0.20978069999999605
>>> min(timeit.repeat(stmt=print_solution, setup=setup, repeat=10))
0.5292953999999952 
```

Enter fullscreen mode Exit fullscreen mode

显然，`print`函数有相当大的开销。换句话说，如果性能很重要，就走`write`路线。否则，`print`很管用！

## 稍微重述一下

好了，这一次就到此为止。查看下面的代码块，获得所有解决方案的列表:

```
# Python 2 only print "Live PD",

# Backwards compatible (also fastest) import sys
sys.stdout.write("Breaking Bad")

# Python 3 only print("Mob Psycho 100", end="") 
```

Enter fullscreen mode Exit fullscreen mode

和往常一样，如果你知道用 Python 在同一行上打印的其他方法，请在评论中告诉我们。同时，为什么不通过以下文章来增长您的 Python 知识呢:

*   [如何在 Python 中反转字典](https://therenegadecoder.com/code/how-to-invert-a-dictionary-in-python/)
*   [如何在 Python 中检查列表是否为空](https://therenegadecoder.com/code/how-to-check-if-a-list-is-empty-in-python/)

如果你喜欢这篇文章或者我列出的任何一篇，可以考虑通过[成为社区的一员](https://www.patreon.com/TheRenegadeCoder)或者[加入邮件列表](https://newsletter.therenegadecoder.com/)来长期停留。否则，我很感激你的支持。谢谢你的来访！

帖子[如何在 Python 中同一行打印:打印和写入](https://therenegadecoder.com/code/how-to-print-on-the-same-line-in-python/)最早出现在[这个叛逆的编码者](https://therenegadecoder.com)上。
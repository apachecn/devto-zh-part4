# 通过向函数添加属性来模仿

> 原文：<https://dev.to/rpalo/mocking-by-adding-attributes-to-functions-5ae0>

我阅读了一篇关于如何快速模拟 print 函数来测试函数对命令行的输出的博客文章，并偶然发现了这种技术。

```
def fake_print(*args, sep=" ", end="\n"):
    fake_print.output.append(sep.join(args) + end)

fake_print.output = []

mymodule.print = fake_print

mymodule.run_function()

expected = """Hello there.
How are you?
I'm great, thanks!
Banana.
"""

assert expected == "\n".join(fake_print.output) + "\n" 
```

Enter fullscreen mode Exit fullscreen mode

所以，首先，我知道有更好更可靠的方法来做到这一点。我的草稿清单上有一个帖子是关于这个的。我还知道，一般来说，如果您必须检查 STDOUT 以确保您的函数正常工作，那么您可以更好地组织您的代码，使其更易于测试。在这种情况下，一个类作业是让我执行一些操作，我将根据我的 STDOUT 输出是否与预期完全匹配来评分，所以我想确切地测试我正在评分的内容，以防我犯了一个愚蠢的打印错误。

不管怎样，我要跑题了。这个帖子的*点*是这样的:

我知道函数是对象。你总是听到他们是“一流”的东西(这意味着他们可以得到更好的小吃，他们的座位可以一直倾斜)。我将它们作为参数传递，将它们赋给变量，在其他函数中定义它们，然后返回它们。

> 我从来没有想过这样一个事实，因为它们是对象，你可以在任何时候给它们添加任意的属性！

现在。下一个合理的问题是:这是个好主意吗？这到底有多大用处？我的回答是:可能没有那么多。

实际上，你在这里所做的一切都是偷偷地把可能是全局变量的东西隐藏在这个函数的属性里面。你这样做是不是增加了一些很好的关于这个变量做什么的自我文档？也许吧。你在清理全局名称空间吗？当然可以。但在大多数情况下，它闻起来有点“聪明反被聪明误”

就像我说的，我从事 Python 编程已经三四年了，并且指导了至少一年，我*从未*见过它。我从来没想到是*。我是正确的最终 Python 知识大师仲裁者吗？不。(好吧，有时候我会告诉我的猫我是。)但是，这似乎是一个很好的指标，表明这种情况并不普遍。我不认为我从聪明中得到的小小的、整洁的名字间距的好处和小小的笑声值得任何其他必须阅读我的代码的人的精神开销。*

 *尽管如此，尽管如此，这仍然是一个巧妙的技巧，突出了我喜欢 Python 的一些地方。这里有另一个展示它的用例:缓存。

```
def fibonacci(n):
    if n in fibonacci.cache:
        return fibonacci.cache[n]

    result = fibonacci(n - 1) + fibonacci(n - 2)
    fibonacci.cache[n] = result
    return result

fibonacci.cache = {} 
```

Enter fullscreen mode Exit fullscreen mode

通过将计算结果保存在`cache` dict 中，可以节省大量的函数调用。

*边注:这真的只是`functools.lru_cache`的蹩脚版本。查一下。太棒了！*

无论如何，我只是想分享这种用 Python 处理事情的简洁、古怪的方式。感谢阅读！*
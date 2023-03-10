# 你应该写无聊的代码

> 原文：<https://dev.to/mjuraj/you-should-write-boring-code-12fd>

我不是一个有几十年编写产品级代码经验的专家，但我是开发多个项目的团队的一员。在那段时间里，我注意到了更多没有经验的开发人员的一些常见误解。

我想分享我发现的最常见的误解。为了做到这一点，让我们来看看我最近做的一个[项目](https://dev.to/mjuraj/side-projects-just-one-more-thing-17c7)中的一个例子:

```
FILTER_KEYWORDS = ("sandbox", "mongo", "postgres", "memcached")
data = [
    line.split() for line in stdout.decode('utf-8').split('\n') if line
][1:]
data = [
    item for item in data
        if any([1 for keyword in FILTER_KEYWORDS if keyword in item[0]])
] 
```

Enter fullscreen mode Exit fullscreen mode

你知道这段代码是做什么的吗？如果有，你花了多长时间？

让我们看看是否能做得更好。

### 让你的代码可读

我指导过不少实习生，这是一个最先引起我注意的常见错误。

让我们从 Robert C. Martin(一个来自开发世界的超级重要的家伙)的话开始:

> 事实上，花在阅读和写作上的时间比远远超过 10 比 1。作为编写新代码工作的一部分，我们不断地阅读旧代码。

想象一下，可怜的人需要在一个月左右的时间里读取你的函数，同时可能试图找到一个疯狂的 bug，它与其他 5 个像你一样的函数交织在一起。

所以，因为他们——停止编写只有你能理解的聪明、复杂的代码。相反，尽量让你的代码简单易懂。这是成为优秀开发者的一部分。

我也容易犯这种错误，但好在我的工作场所有一种不鼓励这种编码的代码审查文化。同样，问问你自己:

*   不太熟练的开发人员会理解这些代码是如何工作的吗？
*   不太了解的开发人员会理解这段代码的作用吗？

让事情变得简单，并且总是试着让某人来监督你。

### 保持代码的可预测性

你写的代码应该看起来总是一样的。这意味着有相同的命名约定、缩进、空白使用等。例如，Python 有自己的风格指南，名为 [PEP8](https://www.python.org/dev/peps/pep-0008/) ，我觉得这是一个很好的开始。

命名尤其重要。给变量起一个好名字真的很重要。让我把这个留给你:

> 计算机科学只有两个硬东西:缓存失效和事物命名。
> 
> 菲尔·卡尔顿

当与其他开发人员在一个团队中工作时，所有这些尤其重要。当每个人都遵循相同的约定编写代码时，预测从哪里*导入函数 X* ，如何*使用函数 Y* 等就变得更容易了。

为了遵守 PEP8 标准，我们使用了一个名为 [flake8](http://flake8.pycqa.org/en/latest/) 的 linter 来防止糟糕的代码进入生产。

### 你的代码就是你的文档

我坚信代码应该是不言自明的。当你必须添加注释来解释某事时，这意味着你的代码太复杂了。尽量简化。

另一方面，将注释作为文档添加是我多次遇到的一个陷阱。保持“文档”最新太难了，你会忘记更新它。

所以试着让你的代码足够简单，这样就足够容易阅读。在那些你真的不能做的情况下，在记录的时候确保精确。

### 这给我们带来了什么？

最后，如果我们应用这个建议，我们会得到这样的结果:

```
FILTER_KEYWORDS = ("sandbox", "mongo", "postgres", "memcached")

data = []
logs = stdout.decode('utf-8').split('\n')

for line in logs[1:]:
    for keyword in FILTER_KEYWORDS:
        if keyword in line:
            data.append(line.split())
            break 
```

Enter fullscreen mode Exit fullscreen mode

这篇文章只是触及了如何编写更好代码的皮毛。想了解更多，真的推荐一本叫做 [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) 的书。
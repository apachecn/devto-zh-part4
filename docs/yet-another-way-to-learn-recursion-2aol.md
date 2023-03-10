# 另一种学习递归的方法

> 原文：<https://dev.to/renegadecoder94/yet-another-way-to-learn-recursion-2aol>

每个学期，我都会进行一次调查，以获得对我教学的一些反馈。在过去的这个学期里，终于有人给了我一个写新文章的想法。特别是，他们想学习更多关于递归的知识，所以我想我应该收集一些技巧。

## 递归概述

对于那些第一次学习递归的人，我想我应该提供一点这个概念的概述。

特别是，递归是一种依赖于解决更小的子问题的问题解决技术。换句话说，我们不是直接解决一个问题，而是继续把问题分解，直到我们能解决一些更小的问题。然后，我们使用最小的子问题的答案来回溯，直到我们得到原始问题的答案。

例如，假设我们要计算 2 <sup>6</sup> 。通常，我们会把它分解成多次乘法。换句话说，我们将 2 乘以 6，得到 64。在计算机科学中，我们称这种问题解决技术为迭代，我们通常以循环的形式看到:

```
def iterative_power(base, exponent): 
  product = 1 
  for i in range(exponent): 
    product *= base 
  return product

print(iterative_power(2, 6)) 
```

Enter fullscreen mode Exit fullscreen mode

当然，如果我们已经知道了一些更小的子问题的答案。例如，如果我们知道 2 <sup>5</sup> 是 32 呢？然后，我们可以通过将 2 <sup>5</sup> 乘以 2 来直接计算 2 <sup>6</sup> 。这就是递归背后的思想:

```
def recursive_power(base, exponent): 
  if exponent == 0: 
    return 1 
  else: 
    return recursive_power(base, exponent - 1) * base 
```

Enter fullscreen mode Exit fullscreen mode

在本文中，我们将看看递归的另一种思维方式。希望它能帮助你比以前更好地理解这个概念。

## 为什么递归这么难？

虽然函数式编程目前正在卷土重来，但它并没有对我们学习编码的方式产生巨大的影响。因此，大多数人开始用类似 C 语言的命令式编程语言编码。例如，我从 Java 开始，然后转向 C、C++、C#和 Python 等语言。

不幸的是，学习像 C 语言这样的语言的缺点是，我们在思考解决问题的方式上有点受限。换句话说，这些语言提供给我们的工具集很大程度上偏向于分支(即 if 语句和循环)。

因此，递归背后的直觉并不是从一开始就建立的。相反，我们被迫使用与迭代解决方案相关的人为例子来引入递归。否则，我们怎么联系。

此外，像 C 这样的命令式语言在引入递归之前需要大量的开销。例如，在开始考虑递归之前，你可能必须学习变量、分支和函数等概念。事实上，直到我上第一堂数据结构课，我才第一次接触到这个概念。

总之，很难掌握递归，因为它要求你彻底理解编码。幸运的是，有办法打破这个循环(双关语*绝对是*有意的)。

## 通过契约设计学习递归

最近，我在俄亥俄州立大学培训教授软件组件课程，他们在课程中涉及的主题之一是递归。当然，那时我已经非常熟悉递归了，但我认为他们教授这门学科的方式非常优雅。因此，我想我可以把这个方法传授给你。也就是说，我们需要首先解决合同设计问题。

### 合同设计简介

如果我有点重复我自己的话，我道歉，因为我在过去写过一些关于契约式设计的文章——[主要参考 JUnit 测试](https://therenegadecoder.com/code/junit-testing-in-java/#design-by-contract)。自然，我认为我现在是一个稍微好一点的作家，但是也可以随意参考那篇文章。

无论如何， **Design by Contract** (DbC)是一种编程哲学，我们从契约的角度来考虑功能。换句话说，当用户使用我们的功能时，我们希望为他们提供某种保证。特别是，我们要求我们的用户在特定的条件下(也就是前提条件)执行每个功能。因此，我们承诺每个函数都将以某种方式运行(也称为后置条件)。

DbC 在递归中很重要，因为它允许我们精确地指定一个函数在特定条件下会做什么。例如，我们之前定义的幂函数只有在我们指定一些前提条件时才起作用:

*   两个参数都必须是整数(可以通过一些类型提示来处理)
*   `power`参数不能为负

因此，我们承诺这两个函数都将返回提升到某个`power`的`base`。如果用户输入了无效的输入，我们不做任何承诺，因为他们显然违反了合同。当然，我们总是可以提供某种输入验证来防止讨厌的错误，但这超出了 DbC 的范围。

### 所有人的免费午餐

在 OSU，我们通过完全忽略递归的概念来引入递归。相反，我们利用我们对契约式设计的了解，开始隐式地实现递归函数。

比如我们再来看看这个我们一直在说的幂函数:

```
def power(base: int, exponent: int) -> int: 
  """Computes the base ^ exponent. 

  Precondition: exponent >= 0 
  Postcondition: base ^ exponent 
  """ 
  return FreeLunch.power(base, exponent) 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们从一个叫做`FreeLunch`的神奇库中引入了一个新的幂函数。就我们而言，这个新的幂函数和我们之前写的完全一样——完全一样的契约。

现在为了论证，假设这个`FreeLunch`幂函数有一个要求:它的输入必须比我们幂函数的输入“小”。我们能做些什么来确保这一点？

好吧，如果我们减少指数，我们可以通过乘以基数(即 x <sup>5</sup> = x <sup>4</sup> * x)把它加回来。让我们试试:

```
def power(base: int, exponent: int) -> int: 
  """Computes the base ^ exponent. 

  Precondition: exponent >= 0 
  Postcondition: base ^ exponent 
  """ 
  return FreeLunch.power(base, exponent - 1) * base 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我们如何去验证这是可行的？好吧，让我们尝试一些输入。例如，我们可以再试试 2 <sup>6</sup> 。如果我们跟踪代码，我们会注意到我们调用了`FreeLunch.power(2, 5)`，这是一个完全有效的输入。换句话说，我们将得到 32，再乘以 2 得到 64，这就是正确答案。

也就是说，有什么输入会失败吗？绝对的！由于`FreeLunch`幂函数与我们的幂函数共享同一个契约，因此存在无效的输入。例如，我们应该避免任何一种情况，即`FreeLunch`幂函数被传递一个小于 0 的值:

```
def power(base: int, exponent: int) -> int: 
  """Computes the base ^ exponent. 

  Precondition: exponent >= 0 
  Postcondition: base ^ exponent 
  """ 
  if exponent == 0: 
    return 1 
  else: 
    return FreeLunch.power(base, exponent - 1) * base 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以确定我们的幂函数起作用了。

### 天下没有免费的午餐

正如您可能想象的那样，以这种复杂的方式实现幂函数只是一种欺骗您使用递归的练习。换句话说，我们可以完全移除上面的`FreeLunch`类，这样我们就有了一个功能完整的递归解决方案:

```
def power(base: int, exponent: int) -> int: 
  """Computes the base ^ exponent. 

  Precondition: exponent >= 0 
  Postcondition: base ^ exponent 
  """ 
  if exponent == 0: 
    return 1 
  else: 
    return power(base, exponent - 1) * base 
```

Enter fullscreen mode Exit fullscreen mode

现在，问题是:为什么我们要花这么大力气引入递归？毕竟，几乎所有其他递归教程都利用了某种堆栈。我们为什么不这样做呢？

虽然使用堆栈来教授递归是完全有效的，但是对于学生来说，这通常是相当麻烦的。例如，想象一下在不使用免费午餐技巧的情况下追踪上面的幂函数。如果我们提供两个随机输入——比如基数为 2，指数为 6——我们将不得不遍历每个幂函数调用，直到找到最小的子问题。理论上，对于一个简单的例子，有六条轨迹！

当以这种方式教授时，当学生在程序中尝试各种输入时，他们经常会发现自己迷失在递归中。如果他们转而[暂停他们的怀疑](https://www.quora.com/Do-experienced-programmers-still-get-confused-while-writing-recursive-functions)并信任合同，他们可以证明他们的功能在一次传递中是有效的。当然，他们需要注意他们的输入([参见](http://comet.lehman.cuny.edu/sormani/teaching/induction.html))。

## 递归根

现在我们已经看到了一些例子，我想稍微谈谈递归的来源。毕竟，递归经常是这个*可怕的*主题，它似乎在计算机科学讲座中方便的时候出现。幸运的是，它有一段更有趣的历史。

还记得我们之前是怎么看幂函数的吗？那不是意外。事实上，有许多数学公式可以递归编写。例如，幂公式如下所示:

> a <sup>n</sup> = a <sup>n-1</sup> * a(如果 n >为 0)

自然地，一个 <sup>n</sup> 可以不断地被分解，直到指数为 1 或 0，这取决于我们希望如何终止我们的递归。在上面的例子中，我们忽略了 n = 1，因为 n = 0 给出了 1，这很好。换句话说，增加 n = 1 的额外情况对结果没有影响。

像幂一样，还有其他几个你可能已经知道的递归公式。例如，斐波纳契数列由一个递归公式定义:

> a<sub>n</sub>= a<sub>n-1</sub>+a<sub>n-2</sub>

为了确保这个公式有效，我们必须定义前两个术语。然后，一切都很好。取决于你问谁，前两个词可能是 0 和 1 或 1 和 1。不管怎样，两对数字都有效。

如果我们想计算序列中的任意项，我们分解原始函数，直到我们遇到任何一个基本情况。例如，下图说明了我们如何计算斐波纳契数列中的第五项:

[![Recursion Tree with Fibonacci](img/13a77fa7799688cbf2e95dd926bcec26.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VN7iES-5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/therenegadecoder.com/wp-content/uploads/2019/08/recursive-fibonacci.png%3Ffit%3D800%252C534%26ssl%3D1)

我们可以整天争论这种递归算法的效率，但这是解决问题的有效方法。我们不是一步一步地解决问题，而是将问题分解成更小的问题，直到找到我们能够解决的问题。然后，我们用这个结果反推，直到得到最终答案。

## 递归结构

既然我们已经通过数学和契约式设计的透镜观察了递归，那么问题就变成了:我们如何识别递归有益的问题？换句话说，有没有一些问题具有某种形式的递归结构？大家说说吧。

事实证明，我们周围到处都有递归结构。例如，斐波纳契数列被明确定义为一个递归公式。当然，还有其他类型的递归结构。例如，上面的树形图是一个递归结构。毕竟，每次调用“fib”都会创建另一棵树。换句话说，树中有树。

在技术之外，还有其他递归结构的例子，如分形，它是几何形状的集合，在任何尺度下都保持其结构。对于那些熟悉[三力](https://zelda.gamepedia.com/Triforce)的人来说，这是一个简单分形的完美例子:由三角形组成的三角形。

同样，也有一些日常递归结构，比如目录(包含文件夹的文件夹)和洋葱(包含洋葱的洋葱)。换句话说，层次结构和嵌套结构通常是递归的良好指示器。

从这些例子中，我们能识别出什么样的模式吗？嗯，当然！这里的关键是观察一个问题的结构。问题本身是由类似的问题组成的吗？如果是这样的话，递归可能是最好的方法。如果没有，尝试其他方法可能更有意义。

## 著名的递归算法

综上所述，我认为出于启发的目的，列出一个著名递归算法的简短列表是有帮助的。欢迎在评论中分享一些你最喜欢的:

*   [合并排序](https://www.algorithmist.com/index.php/Merge_sort)和[快速排序](https://www.algorithmist.com/index.php/Quicksort)
*   [阶乘](https://cs.nyu.edu/courses/fall17/CSCI-UA.0310-005/lecture02_corrbyind.pdf)、[幂](https://www.cs.cmu.edu/~cburch/survey/recurse/fastexp.html)和[斐波那契](https://medium.com/launch-school/recursive-fibonnaci-method-explained-d82215c5498e)
*   [最大公约数](https://www.dyclassroom.com/recursion-algorithm/greatest-common-divisor-gcd)
*   [河内塔](https://www.dyclassroom.com/recursion-algorithm/tower-of-hanoi)
*   [树遍历](https://hackernoon.com/everything-you-need-to-know-about-tree-traversal-algorithms-theory-and-practice-in-java-b01e18d04e8b)
*   [深度优先搜索](https://medium.com/basecs/deep-dive-through-a-graph-dfs-traversal-8177df5d0f13)

至此，我想我们已经涵盖了一切。如果你有文章中没有提到的任何问题，请在下面的评论中提出。

## 见递归

如果你想学习更多关于递归的知识，我推荐你去看看这篇名为[又一种学习递归的方法](https://therenegadecoder.com/code/yet-another-way-to-learn-recursion/)的非常棒的文章(抱歉，我至少开了一个递归玩笑)。否则，谢谢你的来访！

当你在这里的时候，你应该考虑成为 Renegade Coder 社区的一员。我们很小，但我们正在产生影响。如果你还需要时间来决定，你可以通过[我们的邮件列表](https://newsletter.therenegadecoder.com/)保持联系。

如果你想留下来，我还有很多其他的文章给你:

*   [语句和表达式的区别](https://therenegadecoder.com/code/the-difference-between-statements-and-expressions/)
*   [使用模运算的石头剪刀布](https://therenegadecoder.com/code/rock-paper-scissors-using-modular-arithmetic/)
*   [小心 Java 中 String 的 Substring 方法](https://therenegadecoder.com/code/be-careful-with-strings-substring-method-in-java/)

再次感谢你的支持。点点滴滴，积少成多！

另一种学习递归的方法首先出现在叛徒程序员 T2 的文章中。
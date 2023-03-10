# J 语言中定义动词的多种方法

> 原文：<https://dev.to/petecorey/the-many-ways-to-define-verbs-in-j-20ck>

我最近看了一个有趣的 12 音视频，是关于“Euler Gradus Suavitatis”的，这是一个由莱昂哈德·欧拉发明的公式，用来分析计算一个给定音程的协和音，或者直译为“快乐程度”。

[![](img/5991a398d24d8a804f48786498318305.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mHobYDUm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.petecorey.com/img/2019-07-09-the-many-ways-to-define-verbs-in-j/gradus.svg)

在此公式中，`n`可以是任意正整数，其质因数分解表示为:

[![](img/5b0fa0731084fe070b3df32a6581c9f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Afs0nJtf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.petecorey.com/img/2019-07-09-the-many-ways-to-define-verbs-in-j/factors.svg)

实际上，`n`可以是两个或两个以上正整数的任意比值。简单的音程比，比如一个八度，用`1:2`表示，其中`n`就是`2`。对于更复杂的音程，比如完美的五度音(`3:2`)，我们需要找到两个分量的最小公倍数(`6`)，然后将其传递给 Gradus Suavitatis 函数。我们甚至可以对整个和弦做同样的处理，比如大三和弦(`4:5:6`)，最小公倍数是`60`。

## J 中的甜叶菊

出于好奇，我决定使用 J 编程语言实现 Gradus Suavitatis 函数的一个版本。

让我们首先将区间表示为一个整数列表。这里我们从一个大三和弦开始:

```
4 5 6
4 5 6 
```

接下来，我们将找到输入的最小公倍数:

```
*./ 4 5 6
60 
```

从那里，我们想找到最小公倍数的质因数:

```
q: *./ 4 5 6
2 2 3 5 
```

由于我们的质数在因式分解中是重复的，我们可以假设它们的指数总是`1`。所以我们将从每个质数中减去 1，然后乘以 1(这是一个空操作):

```
_1&+ q: *./ 4 5 6
1 1 2 4 
```

或者更好:

```
<: q: *./ 4 5 6
1 1 2 4 
```

最后，我们对结果求和并加一:

```
1 + +/ <: q: *./ 4 5 6
9 
```

我们得到了一个大三和弦的“快乐度”。有趣的是，一个小三和弦(`10:12:15`)给了我们相同的值:

```
1 + +/ <: q: *./ 10 12 15
9 
```

## Gradus Suavitatis 作动词

为了更容易地使用我们新的 Gradus Suavitatis 函数，我们应该把它包装成一个动词。

在 J 中构建动词的一种方式是使用`3 : 0`或`verb define`构造，这让我们可以编写一个多行动词，接受`y`和可选的`x`作为参数。我们可以用`verb define` :
来写我们的 Suavitatis

```
egs =. verb define
   1 + +/ <: q: *./ y
   ) 
```

虽然这一切都很好，但我不高兴我们需要三行来表示我们作为动词的简单功能。

另一种选择是使用[caps(`[:`)]([https://www.jsoftware.com/help/dictionary/d502.htm](https://www.jsoftware.com/help/dictionary/d502.htm))来构建一个动词串，它有效地完成与我们的简单链式计算相同的事情:

```
egs =. [: 1&+ [: +/ [: <: [: q: *./
   egs 4 5 6
9 
```

然而，使用如此多的盖子使得这个解决方案看起来好像我们使用了错误的工具。

[Raul 在 Twitter 上向我展示了光明](https://twitter.com/raudelmil/status/1144665724993986560)并让我意识到单行动词定义语法，我在阅读面向 C 程序员的 [J 初级读本](https://www.jsoftware.com/help/primer/monad_dyad_def.htm)和 [J 时不知何故设法跳过了这一点。使用`verb def`构造，我们可以使用简单的从右到左的计算结构:
在一行中编写我们的`egs`动词](https://www.jsoftware.com/help/jforc/preliminaries.htm#_Toc191734306)

```
egs =. verb def '1 + +/ <: q: *./ y'
   egs 4 5 6
9 
```

原来在 J 语言中有超过 24 种形式的“实体”结构。我希望我能早点知道这些。

不管我们如何定义我们的动词，Gradus Suavitatis 是一个有趣的功能。使用`plot`工具，我们可以绘制从`1:1`到`1:100`以及更远的每个音程比率的“快乐程度”:

[![](img/0d323cea882221c464fa30a7e577935a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TvgBr5kS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.petecorey.com/img/2019-07-09-the-many-ways-to-define-verbs-in-j/plot.png)
# 使用伪代码的经验

> 原文：<https://dev.to/paladique/experiences-with-pseudocode-3i70>

我上过一些编程课程，在这些课程中，我们不写任何代码，直到我们学会如何将我们试图用纸和笔解决的问题概念化。教授们将其称为伪代码:用一种计算机无法编译的语言一步一步地写出算法的预期行为。通常，这将类似于你写的和说的语言。

你可能听说过橡皮鸭方法:你和一些无生命的物体，比如橡皮鸭，讨论你的问题。目的是大声说出问题，以便更清楚地理解你试图解决的问题和/或解决问题所需的算法的更好想法。伪代码是橡皮鸭方法的很好的补充或替代，因为它应用了相同的原理。

## 编写伪代码

在编写伪代码时，确实没有什么严格的规则，但是有一条规则你应该尽量遵守，那就是避免任何特定于语言的语法。你应该可以大声读出来，但是不能在你的电脑上编译。此外，没有规定你用什么来写它。例如，我喜欢在工作日志中用笔和纸来写，但有时会在代码注释中将其打印出来。

当我给学生讲授 web 开发入门时，我们在开始学习 JavaScript 之前先学习伪代码。下面是我教的写伪代码的 4 个步骤:

*   写出步骤
    *写出所有应该发生的事情。我喜欢使用的一个策略是写下第一步和最后一步，然后处理中间发生的事情。*

*   确定可以随时间变化的事物
    *通常，这些被确定为将在代码中使用的变量。*

*   确定条件、决策和可重复的步骤(if/else，循环)
    *这些很可能是控制语法，如代码中的 if/else 语句和循环。*

*   把它们放在一起，组织好
    *如果用笔和纸写出来，你可能会得到一个“草稿”版本。同样的，我们有时需要看看我们的提交历史，拥有你的思维过程和工作的历史是有益的，所以把草稿放在身边！*

*   **奖励步骤** : *别想多了！*
    解决问题的方法是主观的:伪代码是你尝试过的真实方法的书面表达。你是专家。你可以根据自己的喜好描述或简洁。这些步骤只是一个指南，可以按照任何最适合你的顺序来执行。

## 例子

我用类似子弹日记的格式写工作日志，每天留出空间做笔记，包括伪代码。这段伪代码来自我构建的的[样本:](https://github.com/Azure-Samples/functions-dotnet-github-release-notes)

<figure>

[![author's pseudocode](img/fda0f2d4bb544c1ac0ce4413770313d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MyaWrcxx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/spvji32ivw6jsrrw9kmz.jpg)

<figcaption>Pseudocode from my work journal</figcaption>

</figure>

在我的网络开发课上，我写了伪代码来指导学生完成每个家庭作业的步骤。

下面是我布置的作业，我们在这里搭建了一个小的猜谜游戏。用户需要猜一个 1-25 之间的随机数。如果他们猜了 5 次都没猜中，那么他们就输了。如果用户猜出正确的数字，用户将获胜。下面是一个已完成的作业的样子:

[https://glitch.com/embed/#!/embed/tranquil-parakeet?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/tranquil-parakeet?previewSize=100&path=index.html)

下面是作业附带的伪代码:

```
1\. User types guessed number in textbox
2\. User clicks button to guess number
3\. If already guessed more than 5 guesses
    Display “game over, you lost”
    End
   Else
    Compare to winning number
4\. If guess is equal to winning number
    Display “you won, game over”
    End
   Else
    Display “wrong, guess again”
    Back to step 1 
```

## 关闭思绪

如果你正在解决一个复杂的问题，并且想出的问题比答案多，试试伪代码吧！离开代码一步来明确定义问题可以让你更接近解决方案。

PS:通过 [@flippedcoding](https://dev.to/flippedcoding) 查看此详细帖子:

[![flippedcoding image](img/7ca0243baa4cfac5b5f6b3ea7d641afb.png)](/flippedcoding) [## 如何写伪代码

### mile CIA McG Apr 25 ' 193 分钟读数

#webdev #beginners #advice #pseudocode](/flippedcoding/how-to-write-pseudo-code-2jfe)
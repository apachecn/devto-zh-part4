# 作为开发人员，我如何做笔记

> 原文：<https://dev.to/scrabill/how-i-approach-notetaking-as-a-developer-c7a>

最近，我意识到做好笔记的重要性。

> ![Shannon Crabill profile image](img/9f50f8667e76192149f1d9b50cf7c2af.png)香侬·克拉比尔@香侬 _ 克拉比尔![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)当你查阅你关于一个项目/过程的笔记，结果发现你做了非常糟糕的笔记，难道你不讨厌吗？2019 年 7 月 15:12PM-09[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1148610963479900160)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1148610963479900160)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1148610963479900160)

我承认我不止一次这样做过。

我以前对记笔记的态度是“如果我不记得，那一定没那么重要”。

呀。

这在艺术学院可能行得通，因为素描和概念是很常见的。但是，随着我越来越深入到软件工程师这一边，记住*特定的*过程变得更加困难。

首先，需要回忆的不仅仅是这个概念。但是*如何*。

你用的是 Ruby 还是 Javascript？从数组中获取随机项的概念在两种语言中可能是相同的，但语法会有所不同。输出呢，应该是什么格式？或者，这需要说明一个论点吗？

这些是在编写代码时想到的一些事情，这些代码类似于我以前做过的一些事情，但我不记得我是如何做到这一点的。

如果我能把《大力士 中的每一首歌词目前占据的记忆空间转储到我的大脑中，也许我能记住所有的事情，但现在，我会坚持做更好的笔记。

以下是对我有效的方法。

## 字面意思，在你的代码中写注释

我可能不是世界上唯一一个假设未来你会记得你的代码做什么的开发者。这在短期内可能是对的，但长期(几天、几周、几小时等)则是另一回事。

为了解决这个问题，我在注释中向自己解释每一行代码在做什么。

这里有一个例子，使用的方法是[每次运行时随机生成一个新的十六进制颜色](https://github.com/scrabill/hex-color-generator)。

```
def createColor
# Lists all possible integers that can be use in a valid hex code
  hexadecimalIntegers = ["0","1","2","3","4","5","6","7","8","9","a","b","c","d","e","f"] 

# Randomly get 6 items from the hexadecimalIntegers array, then join them together at each character
color = hexadecimalIntegers.sample(6).join("")

# Puts hex color
  "Your new hex color is ##{color}" 
end 
```

Enter fullscreen mode Exit fullscreen mode

写注释时，我喜欢考虑如何向别人解释我的代码。我可以更进一步，添加一个注释来解释为什么我用`.sample`代替`.rand`。

## 手写笔记

我承认，我也想过这是不是我自己想走的路。我保留了一个待办事项的子弹日志，但当从一个数字文档复制粘贴到另一个文档如此容易时，手写代码似乎很愚蠢。

有研究支持[纸笔笔记](https://www.npr.org/2016/04/17/474525392/attention-students-put-your-laptops-away)胜过打字，所以我决定试一试。

当谈到 Ruby 的课程时，我的头都晕了。这是一个有很多活动部件的新概念。在进入高级部分之前，我想确保我理解了核心概念。

在回 Ruby 复习课时，我重读了课程材料，并做了纸笔笔记。任何我发现自己忘记的关键概念——我看着你*实例变量*——我会写下来。类似于我的行内注释，在代码片段的旁边，我会用简单的英语说明这个函数在更大范围内做了什么。

用手记笔记，迫使我保持笔记的简洁和重点。像这样的注释，再加上我的内嵌注释，对重构很有帮助，这就引出了我的下一个观点。

## 经常回顾和重构

你是否曾经解决了一个问题，然后觉得你不知道你实际上做了什么？

也许这是想在短时间内学到很多东西的副作用。但是，我已经意识到，在进入新概念之前，我最好花时间回顾一下我以前写的代码、注释和评论。

这就是重构的用武之地。

重构对我来说是一个新概念，但我认为它是在保持现有代码功能的同时对其进行改进。

通常，我的行内注释提供了足够多的信息，让我知道代码首先要解决什么问题。

然后，我可以参考我的手写笔记——它涵盖了更广泛的概念——看看我可以在哪里进行改进。

比如有没有我可以澄清的变量名？有没有我可以删除的多余代码？有我的代码应该考虑的边缘情况吗？

最后，努力写好笔记可以改变你学习过程中的游戏规则。

虽然书面笔记，辅以内联代码注释对我来说很有用，但重要的是使用最适合你的笔记格式——书面的、打字的、音频的。

学习新概念时，你采取什么记笔记的方法？

[凯莉·西克玛在 Unsplash 上拍摄的照片](https://unsplash.com/photos/viE17T0wtac)

最初发表于我的博客:[https://Shannon crabill . com/blog/taking-notes-as-a-developer/](https://shannoncrabill.com/blog/taking-notes-as-a-developer/)
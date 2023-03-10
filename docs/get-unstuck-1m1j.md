# 放开我。

> 原文：<https://dev.to/brianemilius/get-unstuck-1m1j>

*本文最初发表于[www.brianemilius.com/get-unstuck](http://www.brianemilius.com/get-unstuck)。访问[www.brianemilius.com/blog](http://www.brianemilius.com/blog)了解更多内容。*

* * *

我的应用程序中的一些代码很调皮，不按我说的去做。我真不知道哪里出了问题！我已经有这个问题好几天了。无论我怎么看，我就是看不到出路。我是一个固执的家伙，所以我深挖下去，不断啃这个问题，直到我独自胜利地出现。

我们都试过这个。我们都有一个需要努力解决的问题。我们可能都既讨厌又喜欢这些情况——我知道我喜欢！解决问题是软件开发人员的核心。

那么我们该怎么做呢？我们如何一次又一次地胜利出现？我不知道你是怎么做到的，但我知道我在做什么。这篇文章就是关于这个的。

## 无法读取未定义的属性‘XYZ’

首先，我花了一些时间学习我的语言中不同种类的错误。例如，知道“无法读取未定义的 x”和“未处理的异常”之间的区别对我来说是有回报的。

我已经教会自己分析我在控制台中得到的错误消息，在错误消息提示的地方搜索我的代码，并寻找与错误类型相关的东西。

一个例子！

```
async function getUserByUsername(req, res, next) {
  try {
    const [result] = await conn.query("SELECT id, name FROM users WHERE username = :username", {
      username: req.fields.username
    });
    res.json(JSON.parse(result));
    return;
  }
  catch (error) {
    next(error);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码片段是一个类似 Express 或 Restify 的中间件。如果我弄错了

`Cannot read 'username' of undefined on /path/to/file:4:28`

我知道看什么文件，看什么行。但是我也知道“用户名”不能被读取，因为“用户名”所附着的东西(字段)是未定义的。现在我可以开始探索解决问题的方法了——通过问:“为什么‘字段’是未定义的？我忘了做什么了？”

对我来说，学习不同种类的错误意味着什么可能是调试代码时最重要的事情。

## Console.log(所有的事情)

我的代码中有一堆嵌套的条件，我没有得到我期望的响应。

`console.log("here")`来救援了！

我经常使用这个调试小技巧，你可能会说它是我的首选解决方案。在代码中四处移动`console.log()`。注意它在哪里出现，在哪里没有出现。

这可能看起来很愚蠢，但是你会惊讶这个简单的小技巧是多么的有用(这对于一个点击式标题来说怎么样？).

"但是 IDE 调试器不是已经给了你这个功能吗？"你可能会问。

嗯，是的。确实如此。不过，通常，启动调试器只是为了知道我的逻辑在 JavaScript 中哪里失败了，这有点过分。

话虽如此:

## 和你的调试工具套近乎

所有大的 ide 和 VS 代码都有调试工具。不同的 IDE 使用它们的方式略有不同。但要了解你的。了解断点、调用堆栈、跳转等等。

我在开发时经常在调试模式下运行一个应用程序。这让我可以动态添加断点。

## 休息一下！

真的！做点别的。去散散步，理清思绪。用不同的语言解决不同的问题。帮助别人解决他们的编码问题。无论你做什么，做一些让你分心的事情，把你的注意力从你的问题上移开。

你们中的一些人可能会认为这是一些嬉皮士新时代的建议。但这很有帮助。我保证！甚至有科学支持这种说法。

## 嘎嘎！

橡皮鸭调试是我几乎虔诚地练习的事情。这是一种用语言表达你的问题的方法。你最好对一只橡皮鸭这么做。这种做法背后的理论是，当你必须描述一个问题时，解决方案通常会出现在描述中。我和我的学生经常这样做。事实上，我的一些学生称之为“被布莱恩用橡皮回避”。

## 学会搜索

实话实说吧。谷歌搜索问题的解决方案是我们都会做的事情。或者我们躲起来。可能我们只在 StackOverflow 等特定平台上搜索。不管对你来说是什么情况，学会阐明你的问题的核心。使用关键字。计算机理解像“未定义的错误请求.字段快速”这样的非正常句子(额外的:DuckDuckGo 上的这个搜索词有一个体解析器问题被列为第一结果，顺便提一下，这就是我的例子中的错误)。

## 学会公式化问题

在 StackOverflow 或类似的社区上提问是一个很好的练习。为什么？

你训练自己保持清晰
你可能会帮助其他人解决类似的问题
当你描述你的问题时，你可能会意识到解决方案(橡皮鸭调试数字化)。

## 问

说真的。寻求帮助并不可耻。知道你所知道的。但更重要的是；知道什么时候有你不知道的事情。把它作为学习新东西的机会。问问你的朋友、老师、同事，全能的谷歌霸主。

练习结对编程。我的经验是，成对编程会引起关于代码中“为什么”的大讨论。

## 不用担心

编程很难。编程令人沮丧。训练你的头脑，让它明白失败是一次学习的机会。我们没有从一遍又一遍地擅长同一件事情中学到任何东西。敢于“快速行动，打破常规”，而不是“让我们创建一个有可能推翻政府的应用程序”。

我们都在挣扎。每天晚上。没事的，别担心。这不会变得更容易，但是随着您的学习和成为更好的开发人员，它会变得更有趣。

现在是时候分享你解决编程问题的小技巧和窍门了。你做我做的事情吗？

[封面图片由 www.freepik.com asier _ relampagoestudio-](https://www.freepik.com/free-photos-vectors/water)[制作](http://www.freepik.com)
# 你还没有做过什么事情，现在你觉得你应该做了？

> 原文：<https://dev.to/geocine/what-dev-thing-haven-t-you-done-that-you-feel-like-you-should-have-by-now-356g>

我想分享一下克里斯·塞维利亚在推特上发布的这个问题。

> ![Chris on Code profile image](img/9ed75930e7274b7525d0ffceaba869dc.png)Chris on Code@ Chris on Code![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)有哪些 dev 的事你还没做过，觉得自己到现在应该做了？我有:
> 
> -发布一个 npm 包
> -制作一个 Gatsby 站点
> -在产品中使用 graph QL
> 
> 同样重要的是，不要因为没有做这些事情而感到糟糕/羞愧。按自己的节奏走✌2019 年 8 月 10 日下午 16:36[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1160228432732512256)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1160228432732512256)

我回复了他的推文，他推荐了一些资源来实现我列出的目标。

> ![Aivan Monceller profile image](img/3014af620079a8a30a4828195faa3e64.png)😂)
> -用算法获得自信能力2019 年 8 月 16:37PM-11[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1160591185670356994)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1160591185670356994)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1160591185670356994)

> ![Chris on Code profile image](img/9ed75930e7274b7525d0ffceaba869dc.png)克里斯上码@ Chris oncode![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ aivandroid](https://twitter.com/aivandroid)——我挖第一个。Force be with you
> - Second 很佩服
> 
> 我们得到了一些关于算法的东西由[@ worldclassdev](https://twitter.com/worldclassdev)
> 
> [scotch.io/courses/the-ul…](https://t.co/UfitFKp23o)2019 年 8 月 11 日 18:18PM[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1160616581090594816)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1160616581090594816)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1160616581090594816)

这篇文章是我在 Twitter 上分享的内容的长篇版本。我在 Twitter 上只列出了三个(3)来适应简短的内容。

## 创建自编译编译器

自编译编译器是能够编译自己的源代码的编译器。完成这类事情的过程并不新鲜。这种技术被称为[增强俘获](https://en.wikipedia.org/wiki/Bootstrapping_(compilers))，甚至在 80 年代以前就已经使用了。具有自编译编译器的现代编程语言的几个例子如下:Go、Rust、TypeScript 等。

当创建一个新的编程语言时，你需要创建一个编译器。编译器的初始版本将用不同的(现有的)编程语言编写。一旦你有了一个编译器，你就可以用它把编译器改写成你想要的语言，并编译该编译器的后续版本。

如果真有这种事，我是想一石多鸟。

*   学习对其结构有深入的了解
*   写一个解释器和一个编译器来理解我们写的代码如何被转换，以便计算机能够执行它
*   学习我以前从未遇到过的编程范式并实现它。这将为在我的头脑中打开新的神经通路铺平道路。

为了实现这一点，我正在遵循由 *Thorsten Ball* 写的书:[在 Go 中写解释器](https://interpreterbook.com/)和[在 Go 中写编译器](https://compilerbook.com/)。在我看来，这些书非常简洁，清晰，简短。它解释了复杂的概念，但没有太多的学术性。作者不使用任何第三方库或框架，只使用标准的 Go 库。因此，你可以用任何你熟悉的编程语言来阅读这本书。它还以测试驱动的方式逐步实现解释器/编译器的各个部分。

## 网站开发工作流程

你知道这种感觉吗？你在某方面很专业，但由于种种原因，你无法向朋友或其他人提供你的专业知识。

我的整个职业生涯都在开发网站，但在向客户交付有价值的东西方面，我无法与网络开发工作室的速度相抗衡。

这并不意味着我不能。我在工作中做的事情不是整天转公司网站。当涉及到为某人建立一个网站时，我也不知道该用什么。我没有一个清晰的工作流程，如何从零到生产旋转网站。

当你建立一个基于内容的网站时，你必须考虑三种人:开发者、内容创建者和观众。

你需要有良好的开发经验，这样你就可以很容易地把网站交给另一个开发者。您还应该使内容创建者使用起来简单。内容创建者不应该需要开发人员来创建内容或任何合理的定制。

考虑到您的受众，您应该考虑到人们应该能够在不同的设备上查看网站。你需要考虑可访问性和用户体验。

作为几个非营利组织的一员，我一直真诚地希望帮助他们创建自己的网站。我也想帮助小企业家在网上做生意。

为了实现这一点，我目前正在探索[前端开发者的无服务器能力](https://serverless.css-tricks.com/)和 [JAM Stack](https://jamstack.org/) 。

## 对算法充满信心

当算法的话题开始起作用时，我的冒名顶替综合症最严重。

我有计算机科学背景，过去甚至参加过 ACM 竞赛。尽管如此，我看不到自己在任何算法面试中表现出色。

不知何故，我说服了自己，学习算法很重要，我目前正在使用以下资源:

*   [算法设计和技术@edX](https://www.edx.org/course/algorithmic-design-and-techniques)
*   [JavaScript 数据结构和算法](https://www.goodreads.com/book/show/41188653-javascript-data-structures-and-algorithms)
*   程序员和其他好奇者的图解指南
*   [冒名顶替者手册](https://bigmachine.io/products/the-imposters-handbook)
*   [面试蛋糕](https://www.interviewcake.com/)——对尝试这个有了新的想法。

你呢？你还没有做过什么事情，现在你觉得你应该做了？
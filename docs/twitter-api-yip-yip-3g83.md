# Twitter API，Yip Yip！

> 原文：<https://dev.to/kcarrel/twitter-api-yip-yip-3g83>

我一直想深入研究 Twitter API，原因肯定与构建我自己的 Twitter 机器人大军无关，最近一次关于添加多余的“世界构建”细节有时会产生可疑结果的对话激发了我的灵感。

当然，这就是我所说的那条被诅咒的推文。
[![This cursed image](img/18a47a17f28095b29eb015d293239465.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--H_3t5WWP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7nql1pttpgi51hedvzln.png)

这又增加了什么？为什么这曾被允许在推特上发布？这应该是我下一个个人项目的灵感来源吧？我不知道前两个问题的答案，但后者会得到一个响亮的“当然，为什么不”！

我决定，这个项目的基本目标是模仿一个创造者的 twitter，这个创造者不断地在他们的系列“佳能”中重复或添加内容。我选择《阿凡达:最后的气宗》( ATLA)作为我的推特项目的原始资料有两个主要原因:

1.  《ATLA》是一部完美的动画剧(编者注:最初我有一个充满激情的解释，但实际上我想我可以就这六个字打住)
2.  有一个不错的 ATLA API——谢谢 Paige Gorry！

尽管我在这篇博客的开篇说了一句转移注意力的话——这个个人项目不是我虚构的机器人军队的 twitter 机器人，而是更类似于一个随机的(有点)tweet 生成器。对于这个项目的意图(制作古怪的推文来增加/转发情节点)，我认为制作一个“观众”可以与之互动的前端会很有趣，这就是我如何结束这个项目的。

## Heroku 上的  Tweet 生成器

[ALT ALTA Tweet 生成器](https://alt-atla.herokuapp.com/)
[![Tweet Generator hosted on Heroku](img/04fa3d042abc45c4e625700c054e8e4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MenqtRDh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tr2p5si5ghn1qbdui79j.png)

## 在 Twitter 上生成推文

[ALT ATLA 推特](https://twitter.com/ALTALTA7)
[![Generated Tweet on Twitter](img/e02b11817e82f1931d84be6f8a25cf93.png)T5】](https://res.cloudinary.com/practicaldev/image/fetch/s--dO_EKdNV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wly6v0leump8agkggufl.png)

## 工作原理

Tweet 生成器前端是用 React 构建的，Twitter 处理后端是 Ruby on Rails。

在高层次上，它是这样工作的:

1.  点击生成按钮，Math.rand(omly)将调用 19 个可能的 tweet 模板函数中的一个
2.  根据点击了哪个 tweet 模板函数，将向 [ATLA API](https://github.com/paigeegorry/last-airbender-api) 发出获取请求，以获取一个或两个随机字符名称和/或状态，并设置相应的模板消息
3.  带有我们状态消息的 POST 请求将被发送到我们的 Ruby on Rails 后端进行处理
4.  在后端接收消息将触发一个功能，通过 Twitter API 发布我们的消息
5.  推文现已上线！

您可以在这里查看两者的代码:

*   [前端 github 回购](https://github.com/kcarrel/JK-ATLA)
*   [后端 github 回购](https://github.com/kcarrel/JK-ATLA-BE)

# 我最喜欢的一些推文

[![Secret Aang Family](img/629fb564e3dbcd0ef3f2fb8eb2f3b607.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--czadYl9B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x8kyii5wuxmuc3s5iawx.png) 
[关于这份家谱，有很多事情都被忽略了](https://avatar.fandom.com/wiki/Pik_and_Pak)

[![Hama's revenge against the cabbage merchant](img/67d7cbbd7b876ec33487fb738ad0c703.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2WhoIpOY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0wtf7gtmet58qedk8top.png)

[![Bosco the bear](img/ee6e2b5c95dbf8f6d72927afb9a4a02a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--umo1WdQX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/126s3wd84ubd4qridtc8.png)

[![The Boulder crumbles](img/3cd3d38f16ea3e37ed355b0cfdbea658.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Cqch9VfA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ld942lo50dksi81uqvho.png)

[![Steely Bois](img/2d35a2cf8f2c3f79268a1351d7901918.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j2Xj5zMR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ixlynwquxqrg7xnrs3vl.png)

# 资源

*   [Ruby 中的 Twitter API 设置](http://www.mattmorgante.com/technology/505)
*   [ATLA API](https://last-airbender-api.herokuapp.com/)
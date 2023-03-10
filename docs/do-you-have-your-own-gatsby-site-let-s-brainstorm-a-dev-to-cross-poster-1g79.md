# 你有自己的盖茨比网站吗？让我们集思广益，设计一个交叉海报

> 原文：<https://dev.to/irreverentmike/do-you-have-your-own-gatsby-site-let-s-brainstorm-a-dev-to-cross-poster-1g79>

所以，像所有好的事情一样，这篇文章的想法来自一条推特:

> ![Tierney 📍 @NodeConfCo 🇨🇴 profile image](img/aa014b7002ae3c9132921571b49058c5.png)蒂尔尼📍@ nodeconfco🇨🇴@ bitandbang![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ reverent Mike](https://twitter.com/irreverentmike)[@ amberey](https://twitter.com/amber1ey)[@ CEO _ Noor](https://twitter.com/CEO_Noor)[@ gatsbyjs](https://twitter.com/gatsbyjs)[@ the practical dev](https://twitter.com/ThePracticalDev)既然有了[@ the practical dev](https://twitter.com/ThePracticalDev)API...有没有人建一个盖茨比插件什么的自动发新帖的？🤔2019 年 6 月 19 日上午 03:14[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1141182332809940992)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1141182332809940992)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1141182332809940992)

这是我思考了一段时间的事情。我甚至试着建造它——几次！我不断开始工作，迷失在数百万个方向中。

以下是我想要的:当我在我的盖茨比网站上发布一个新帖子时，我希望这个帖子自动出现在 dev.to 上。

# 应该是:

*   从我的帖子的原始降价源一字不差地重新创建我的帖子
*   使用`canonical_url` frontmatter 字段来确保我在帖子中保留 SEO 的优点
*   没有我的干预就发生了
*   不知道我的帖子来自哪里。我使用 [takeshape.io](https://takeshape.io) 作为我网站的无头 CMS，所以我的个人帖子不受源代码控制
*   只接受我的`master`分支的帖子，因为我经常使用功能分支来起草帖子
*   当然，使用 API 的[开发工具](https://dev.to/api)

# 很高兴有:

*   也许帖子应该先设置为`published: false`，这样我就可以根据需要预览/查看/更新它们了 dev.to？

# 可能的方法:

1.  以某种方式进入 GitHub 的 webhooks，然后在这里发帖
2.  可能是从 CI 之后开始的工作吧？我使用 CircleCI，但是希望这个特性与实现无关。
3.  用一个 [GitHub App](https://developer.github.com/v3/apps/) ？
4.  还是一个 [GitHub 动作](https://github.com/mbifulco/thepraticaldev-crosspost-action)？
5.  也许只是一个部署到 AWS 的 lamba 函数或者 [Netlify 函数](https://functions.netlify.com/)。

所以-这就是我请求你帮助的地方。当然，这里有一个我没有想到的简单方法，或者可能是收集一些场景并基于 80/20 构建一些东西。

我还遗漏了什么？我很想得到你的帮助。对于来自 gatsby 站点的交叉发布者，你会有哪些用例？你将如何实现它？我们怎样才能互相帮助？
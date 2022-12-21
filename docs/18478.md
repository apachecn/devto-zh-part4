# 微前端:深入了解最新的行业趋势。

> 原文：<https://dev.to/bettercodingacademy/micro-frontends-a-deep-dive-into-the-latest-industry-trend-3i7a>

*图片来自[https://medium . com/@ tomoderlund/micro-frontends-a-micro service-approach-to-front-end-web-development-f 325 ebdadc 16](https://medium.com/@tomsoderlund/micro-frontends-a-microservice-approach-to-front-end-web-development-f325ebdadc16)*

Patrick 是我在 web 开发行业的一个朋友，他最近提醒我微前端设计模式的存在，特别是它正在产生的迷你 Twitter 风暴:

> ![Dan Abramov profile image](img/17c386e6839df2dd490f297a2c139b1e.png)丹阿布拉莫夫[@丹 _ 阿布拉莫夫](https://dev.to/dan_abramov)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)微前端我不懂。2019 年 5 月 26 日上午 03:48[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1132493678730252288)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1132493678730252288)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1132493678730252288)

> ![Dan Abramov profile image](img/17c386e6839df2dd490f297a2c139b1e.png)丹·阿布拉莫夫[@丹 _ 阿布拉莫夫](https://dev.to/dan_abramov)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)他们应该解决的问题在我听来好像已经被一个好的组件模型解决了。那么这是在解决组织问题而不是技术问题吗？例如，如果两个团队不能就任何事情达成一致，甚至共享基础设施。2019 年 5 月 26 日上午 03:56[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1132495687957012481)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1132495687957012481)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1132495687957012481)

不偏不倚地说，在栅栏的另一边也有许多人支持这种模式:

> ![Joel Denning profile image](img/ec22049a0976665e548b2997f0b66115.png)乔尔丹宁@乔尔丹宁![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@丹 _ 阿布拉莫夫](https://twitter.com/dan_abramov)我是一个微前端框架 [@Single_spa](https://twitter.com/Single_spa) 的维护者。
> 
> 它们是浏览器中的微服务。这意味着单独构建和部署的前端应用程序可以选择自己的框架和库。
> 
> 目的是组织伸缩，避免框架锁定。2019 年 5 月 26 日上午 06:02[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1132527290993270784)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1132527290993270784)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1132527290993270784)

我和 Patrick 坐下来，我们深入探讨了这个问题，评估了争论的双方。你可以在下面找到完整的视频:

[https://www.youtube.com/embed/D3wyNyXFxJA](https://www.youtube.com/embed/D3wyNyXFxJA)

编码快乐！

* * *

这是 Tech Red Pill 的第一集，在这个播客中，我们谈论并分享我们对紧迫问题和技术领域最新消息的看法。请让我们知道您的想法和/或您希望看到的未来！
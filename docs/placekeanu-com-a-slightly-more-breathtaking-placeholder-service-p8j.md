# PlaceKeanu.com——一个稍微令人激动的占位符服务

> 原文：<https://dev.to/alexandersandberg/placekeanu-com-a-slightly-more-breathtaking-placeholder-service-p8j>

你知道当你在做一个网站开发项目时，需要一张 300x250px 像素的图片吗？

或者，当项目仍在开发中时，您可能需要一个占位符来放置您的头像照片。

虽然已经有了一大堆令人惊叹的选择( [picsum.photos](https://picsum.photos) 、【placeholder.com】T3、[placekitten.com](https://placekitten.com)，仅举几个例子)，但我感觉还缺少点什么...🤔

这个周末，我把一些东西放在一起，让每个人的网站开发过程变得更加令人激动。有些人可能已经在昨天的 ProductHunt 上看到了它[，但是因为每个人似乎都非常喜欢它，所以我也想和你们分享一下。❤️](https://www.producthunt.com/posts/placekeanu)

* * *

[PlaceKeanu.com](https://placekeanu.com)是一个占位符图片服务，基于*山谬·里维*！

[![Keanu Reeves wink](img/52e032b9c22ee90423035e8fd2af78b4.png)](https://i.giphy.com/media/NUwoRZzHc2Bws/giphy.gif)

我是说看看他。这怎么可能已经不是一件事了呢？

## 那么，如何使用这个 lifechanging (ymmv)服务呢？

只要以这种格式粘贴一个链接，在任何你需要的地方都可以:

`https://placekeanu.com/[width]/[height]*/[options]*`
(* optional)

如果你没有传入一个高度，那么尺寸将会是一个基于`width`的正方形。

## 可用的【选项】

*   年轻的基努斯只有:`y`
*   灰度基努斯:`g`

## 下面是一些例子

*   年轻的基努:[https://placekeanu.com/500/300/y](https://placekeanu.com/500/300/y)
*   方形+灰度:[https://placekeanu.com/250/g](https://placekeanu.com/250/g)
*   年轻+灰度:[https://placekeanu.com/300/550/yg](https://placekeanu.com/300/550/yg)

因此，如果你想在你的网站上使用一个基努，你可以用一个`<img>`来包含他，就像这样:

`<img src="https://placekeanu.com/350/500" alt="">`

不错吧。

[![Keanu Reeves thumbs up](img/4c5f00be4e669e90c04a1895a55a9ead.png)](https://i.giphy.com/media/j5QcmXoFWl4Q0/giphy.gif)

## Woot！你是如何建造这件精美的艺术品的？

虽然大多数占位符图像服务会为每个请求动态创建图像，但我发现了一种使用 SVG 实现相同结果的方法。

基本上，当你访问一个页面，比如说`/200/300`，就会返回一个带有`width="200"`和`height="300"`的 SVG，以及一个随机的基努图像。所有的图像都用 Base64 编码，以使其正常工作。

还有一些其他的魔法，比如自动居中和根据你的要求选择垂直或水平的基努，这样他就不会经常被切断。

该项目的源代码当然可以在 GitHub 上获得，所以请[在这里查看](https://github.com/alexandersandberg/placekeanu.com)！🙂

* * *

我很想听听你对此的想法，以及你可能有的任何功能要求。在下面发表评论，或者在 Twitter 上给我发推文( [@alexandberg](https://twitter.com/alexandberg) )！

如果您想更深入地了解我是如何构建的——使用 Express 和 Netlify 函数——请告诉我，我会写一些东西！

但是现在，请欣赏可爱健康的基努！❤️

[![Keanu Reeves blow kiss](img/d864bf9df39ba5b25fec640079111bc8.png)](https://i.giphy.com/media/QAsBwSjx9zVKoGp9nr/giphy.gif)

* * *

哦，顺便说一句，我刚刚创建了一个[列表](https://dev.to/listings/forhire/looking-for-company-to-work-with-on-master-s-thesis-project-in-front-end-ux-and-or-accessibility-1d4l)，里面有一个非常有趣的(*，对我来说，是改变人生的*)机会。如果你能分享我的微博，这对我意味着一切。☺️

> ![Alexander Sandberg profile image](img/20f550f0d2154200adffe67bcb9d3882.png)亚历山大·桑德伯格@亚历山大伯格![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我刚刚在 [@DEVListings](https://twitter.com/DEVListings) 上贴了一个房源！👀
> 
> “寻找与 UX 硕士论文项目合作的公司，和/或无障碍”
> 
> [dev.to/listings/forhi…](https://t.co/7fPmn3QpOv)
> 
> 这将是我职业生涯和生活的下一步。如果有人分享这一点，我将永远感激。❤️2019 年 7 月 11 日 22 点 17 分[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1149442625113665536)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1149442625113665536)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1149442625113665536)
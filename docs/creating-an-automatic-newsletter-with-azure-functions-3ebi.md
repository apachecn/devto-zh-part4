# 用 Azure 函数创建自动时事通讯

> 原文：<https://dev.to/halldorstefans/creating-an-automatic-newsletter-with-azure-functions-3ebi>

照片由来自 Pexels 的 Symeon Ekizoglou 拍摄

首先，我想感谢 [dev.to](http://dev.to) 提供了这个奇妙的平台，还有 Burke Holland

[![burkeholland image](img/48a31caeb237a0669d58aae0555350e2.png)](/burkeholland)

## [伯克荷兰](/burkeholland) <button name="button" type="button" data-info="{&quot;id&quot;:57943,&quot;className&quot;:&quot;User&quot;,&quot;name&quot;:&quot;Burke Holland&quot;,&quot;style&quot;:&quot;full&quot;}" class="crayons-btn follow-action-button whitespace-nowrap  " aria-label="Follow User: Burke Holland" aria-pressed="false">跟随</button>

for sharing [this series](https://dev.to/azure/create-an-email-subscription-with-azure-functions-part-1-1m0k). I probably wouldn't have been able to finish this project if I hadn't found this.

## 最初的想法

在冰岛，有一个足球新闻网站叫做[fotbolti.net](http://fotbolti.net)。这是冰岛最受欢迎的足球新闻网站。每当一名球员转会完成，或者一名教练被雇佣或解雇，fotbolti.net 总是在他们的标题中有“staf-fest”(确认)这个词。我相信他们有 100%的准确率。也就是说，他们从未发表过标题中带有“staf-fest”的不合法的新闻文章。

因此，由于欧洲的转会市场非常开放，并且正在发生很多事情，我有了一个想法，创建一个时事通讯，只给我发送标题中有“stafest”的新闻。我通常一天去几次 fotbolti.net，因此通常不会错过这条新闻。然而，当我去度假、生病或忙碌一天时，我不会经常访问它(有时根本不访问)，所以这时候简讯就可以派上用场了。另外，我只是觉得尝试无服务器可能是一个有趣的项目。:)

## 概念证明

我决定创建一个小的 [Python 程序](https://github.com/halldor90/fotbolti.net_stadfest_rss)作为概念验证。

为了完成我的概念验证，它需要满足以下要求:

*   获取 RSS 源
*   仅获取前一天(24 小时)的新闻
*   只获取标题中有“staf-fest”的新闻
*   打印出标题

一切都很顺利，我没花多长时间就完成了这一部分。在我完成了概念验证之后，我对自己说，“现在怎么办？”。我不知道如何收集电子邮件地址，在某种数据库中获取地址以发送实际的时事通讯，或者如果有任何新闻要报道，我如何确保程序每天在同一时间运行。

我从一开始就很确定我想为此使用 Azure 函数，主要是因为我想了解更多。我发现用 Python 创建一个函数是可能的，因为我用 Python 做了概念验证程序，所以我决定尝试一下。

不幸的是，我从来没有能够得到正确的工作。那是我开始大量“谷歌搜索”的时候。我确信有人已经完成了一些 Azure 功能并在互联网上分享了它们。它不需要完全像我的想法，只是某种 Azure 函数，我可以基于它来产生我的想法。

## [dev .](http://dev.to)来救援

就在那时，我看到了 Burke Holland 在 dev.to 上分享的 Michael Crump 的“Azure Tips and Tricks”系列文章。我简直不敢相信我的运气。这个系列包含了我想做的所有事情的信息。起初，在我看来，我唯一需要做的改变就是将我的 Python 代码改为 C#代码并使用它。

在经历了指南中的所有步骤后，我的代码中出现了一些语法错误。我不明白为什么，因为我做的一切都是一样的。因此，我尝试了不同的名称空间、方法、库，但似乎都不起作用。终于在一个周日的下午，在花了我自周四以来所有的空闲时间(没有时间和我的女朋友在一起)之后，我设法把这个问题解决了。我是如此的兴奋和快乐。(因为某些原因，我的女朋友没有我这么热情)。但是他妈的成功了。

[![](img/8305b83f6b460abe15fb876f2ea4bfda.png)](https://i.giphy.com/media/5GoVLqeAOo6PK/giphy.gif)

* * *

在清理完所有东西并使之变得漂亮之后，我请求网站允许我使用 RSS 并让我的项目继续下去。他们允许我使用它，为此，我很感激。

感谢阅读，这里是 [GitHub](https://github.com/halldor90/AutomaticEmailNewsletter) 上的最终产品。

如果你懂冰岛语，想订阅时事通讯，[这里有链接](https://fotboltinet.z6.web.core.windows.net/)。

对于阅读这篇文章的每一个人，请继续与世界分享你的技巧和诀窍。你永远不知道什么时候你可以帮助别人。

* * *

感谢您的阅读。对于我的每周更新，你可以注册我的[时事通讯。](https://email.halldorstefans.com/)
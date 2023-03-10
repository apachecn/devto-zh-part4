# 前端新闻#4:谷歌云中断，Chrome 75，登录谷歌和 Firefox Premium

> 原文：<https://dev.to/adriansandu/front-end-news-4-google-cloud-outage-chrome-75-sign-in-with-google-and-firefox-premium-358f>

[![](img/a0e1c029e037a13c770e4b1fc641d184.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rYXT86iH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ALhILtEe-3YWzgxEx_rzr7Q.png)

大家好，欢迎来到另一个版本的前端新闻，为您带来业内最新事件的信息。

让我们看看我为你准备了什么:

*   大规模宕机影响了谷歌云服务
*   Chrome 75 已经发布
*   在 WWDC 宣布“登录苹果”
*   Firefox 将在 10 月推出高级版本

就像上次一样，我也为那些喜欢听而不是读的人准备了一个视频版本。如果你喜欢这种格式，如果你也能订阅 YouTube 频道，我将不胜感激。

[https://www.youtube.com/embed/yJR-hkhGIG0](https://www.youtube.com/embed/yJR-hkhGIG0)

### 谷歌云平台大停电

6 月 2 日周日，谷歌云平台遭遇了一次重大故障。因此，美国东部的大片地区在访问依赖于该平台的服务时遇到了问题，如 YouTube、Docs、Gmail、Drive 和套件的其余部分。此外，其他第三方服务也受到了影响，如 Snapchat 或 Discord，因为它们也在后端使用谷歌云。

第二天我们收到了本杰明·特雷诺·斯洛斯的官方声明。你可能会问，这个人是谁？他是谷歌的工程副总裁，他在 LinkedIn 上的描述以“如果谷歌停止工作，那是我的错”开头。

<figure>[![](img/167c57b0501900cc1837c909df78ad02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T-LFPI_v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Amu1_7O2zehZjQ62s5xi5Lg.png) 

<figcaption>本杰明·特雷诺·斯洛斯在 LinkedIn 上的简介:“如果谷歌停止工作，那是我的错”</figcaption>

</figure>

所以，如果你受到影响，责怪他。无论如何，他概述了问题最初是如何开始的，团队是如何应对这个问题的，并承诺在考虑到所有因素后，在官方的事后分析中提供更多细节。

正如所承诺的那样，这份验尸报告于 6 月 6 日发表，内容详尽。长话短说，在一次计划维护事件中，内部网络管理在一个比预期大得多的区域断开。这导致了剩余网络连接的严重拥塞，进而延迟了校正命令的部署。这就是我们许多正常人在严重的高峰时间交通堵塞时所经历的。阳光下无新事。

<figure>[![](img/5c5d95804f408d534c323682623d0245.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--voDLKVUK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/480/1%2ApRiJT3yN0Sc8f0fbbI_xUA.gif) 

<figcaption>堵车截图来自《辛普森一家》第 14 季 ep。15 经 giphy.com</figcaption>

</figure>

如果你想了解完整的故事，你可以在下面的链接中找到。

来源和参考资料:

*   [https://cloud . Google . com/blog/topics/inside-Google-cloud/an-update-on-sundays-service-disruption](https://cloud.google.com/blog/topics/inside-google-cloud/an-update-on-sundays-service-disruption)
*   [https://status.cloud.google.com/incident/compute/19003](https://status.cloud.google.com/incident/compute/19003)
*   [https://status . cloud . Google . com/incident/cloud-networking/19009](https://status.cloud.google.com/incident/cloud-networking/19009)
*   [https://status . cloud . Google . com/incident/cloud-networking/18012](https://status.cloud.google.com/incident/cloud-networking/18012)

<figure>[![](img/b404012d08b1a5641b40548eaf700567.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VP-oz7Bq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AzdOanFXF6sXcTTSQcALO6g.jpeg) 

<figcaption>图片由谷歌 Chrome 开发者 YouTube 频道</figcaption>

</figure>

提供

### Chrome 75 发布

Chrome 版本来到了我们的桌面，带来了一系列更新。以下是最重要的几个:

*   使用`desynchronized`提示标志减少画布元素的延迟
*   使用 Web Share API 共享音频文件、图像、视频和文本文档的新支持
*   现在，您可以使用下划线作为大数字的分隔符，使它们更加明显

作为奖励，Chrome 开发者团队在 Google I/O 2019 上的所有演讲都可以在该团队的官方 YouTube 频道上看到。

来源:

*   [https://developers.google.com/web/updates/2019/06/nic75](https://developers.google.com/web/updates/2019/06/nic75)

<figure>[![](img/afc42b36fa1201e310b174bc1e5ad8c8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k_VjgFuU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ALOvz-CY_OiZJYDsiS7hoxQ.png) 

<figcaption>图片由 Mozilla 博客</figcaption>

</figure>

提供

### 火狐计划推出付费高级服务

Mozilla 公司首席执行官 Chris Beard 接受了德国杂志 [T3N](https://t3n.de/news/mozilla-ceo-chris-beard-anbieten-1168614/3/) 的采访。他证实，他经营的公司正在考虑扩大收入来源，并考虑将订阅服务作为一种选择。别担心，你不用付费就能使用 Firefox。该浏览器将继续对所有人免费开放。相反，计划中的 Firefox Premium 将包括一个 VPN 解决方案和扩展的云存储，旨在增加您在线体验的安全性和隐私性。

还会有一个“先试后买”的选项。基本层将是免费的，每个人都可以访问，尽管它对存储空间和 VPN 服务包含的流量带宽都有限制。如果您对所提供的体验感到满意，您可以付费进入高级等级，这将为您提供更大的限制。

资料来源(原文为德语):

*   [https://T3 n . de/news/Mozilla-CEO-Chris-beard-1168614/3/](https://t3n.de/news/mozilla-ceo-chris-beard-anbieten-1168614/3/)提供

### 登录 2019 WWDC 发布的苹果

苹果的 WWDC 2019 是科技界的一件大事。当每个人都在讨论有史以来最昂贵的奶酪刨丝器时，也有一个公告将影响前端开发行业。

<figure>[![](img/347a82ddb06a1a540a830b796960fbc7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H22upUBg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZeyHIGbEAoo1_Q4n8JkRtA.png) 

<figcaption>来源:[https://twitter.com/JJMcCann/status/1135612816558317570](https://twitter.com/JJMcCann/status/1135612816558317570)</figcaption>

</figure>

苹果推出了新的“登录苹果”服务，作为脸书、谷歌或推特提供的类似服务的替代品。它实际上将成为应用商店中应用程序的一个要求，但是它也可以在网络上使用。

<figure>[![](img/398c6a56f56927b8c82be0af4fc8f8cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I1cULv5a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AlxPDzA6Cvy85IRzF687I4A.png) 

<figcaption>来源:[https://developer.apple.com/sign-in-with-apple/](https://developer.apple.com/sign-in-with-apple/)</figcaption>

</figure>

TechCrunch 对这项新功能进行了真正深入的分析，并为任何开发人员可能遇到的最常见问题提供了答案。更多细节可以在下面链接的文章中找到。

来源:

*   [https://TechCrunch . com/2019/06/07/answers-to-your-burning-questions-about-how-sign-in-with-apple-works/](https://techcrunch.com/2019/06/07/answers-to-your-burning-questions-about-how-sign-in-with-apple-works/)

### 今日问题

你有没有建立一个依赖谷歌云平台的应用程序，它受到了宕机的影响？你亲身感受到这一事件的影响了吗？请在下面的评论区告诉我们。

这一版就这么多了。在 https://twitter.com/frontendnexus[的 Twitter 上关注 Front End Nexus](https://twitter.com/frontendnexus)，一旦有新的更新，我们将立即通知您。我还想鼓励你在 https://www.youtube.com/channel/UCgACtqiDmnSaskDIBsK54ww[的](https://www.youtube.com/channel/UCgACtqiDmnSaskDIBsK54ww)订阅 YouTube 频道。一旦该频道达到 100 个订户，我就可以获得一个自定义 URL，因此非常感谢您的支持。

祝您度过愉快而富有成效的一周，我们下次再见！

* * *
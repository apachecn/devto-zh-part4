# 介绍 Chill:一个开源的服务监控工具

> 原文：<https://dev.to/leapfrogtechnology/don-t-panic-just-chill-101e>

* * *

你已经有一段时间没有听到我们关于 Chill 的消息了。我们[上一次更新](https://blog.lftechnology.com/introducing-chill-e2bd5053aebf)是将近两年前。唷！你们所有人可能在某个时候都有过创造新事物的冲动，只是为了让这种兴奋慢慢消失。我们自己也经历过类似的情况。😉

这是背景故事，几个月前，我们发现我们构建的工具已经在生产中运行了一年多。它仍然在监控我们的一些生产网站，没有任何问题。我们并不感到惊讶，因为我们会不时在我们的 Slack 频道上收到来自 Chill 的停机通知。有人喜欢说，这个简单的“ping 工具”在没有太多监督的情况下运行得非常好。这就是为什么我们决定通过增加新功能和坚持我们在项目开始时采用的最小化方法来进一步发展。

现在是 2019 年，这里是我们对“Chill”-我们最小且可靠的服务监控工具-所做的一些更改。

**稳定版**
我们在 2019 年 3 月 12 日悄悄发布了我们的稳定版 Chill v1.0.0。自从发布以来，我们已经对 Chill 及其内部做了相当多的改变。

**新标志**
[![](img/578275088128765b7c796793657a4542.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vzbD7cFD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p61sggoixndz8hnlk7tc.png) 我们新标志的第一个变化就是寒冷。我们一直希望 Chill 有这种放松的感觉，因为它一直关注着我们的服务。尼泊尔语中的“Chill”一词翻译过来是一只鹰，它是一个完美的吉祥物，可以很好地观察我们的服务。难怪我们的标志是一只戴着酷酷墨镜的鹰。尼泊尔语和英语的混合意思是“寒冷”,给这个标志带来了生命。这就是我们如何得到“寒冷”这个名字的。

新的状态面板
我们改进了状态面板，增加了一些项目。首先，我们在状态报告下面增加了一个名为“过去事件”的新部分。页面的这一部分包括按日期分组的不同服务的任何过去事件或停机时间信息。同样，整个页面的设计也做了一些调整，使其看起来简洁明了。

[![](img/9f158d567411daac6ca02c0dc79e135a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ssmAEoVg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w0al4nctbdvgp9krjg7t.png) 
*寒意状态仪表盘*

**支持 HTTP 基本认证**

Chill 1.1.0 增加了对 HTTP 基本认证的支持。在以前版本的 Chill 中，如果您的网站/应用程序在 [HTTP 基本认证](https://en.wikipedia.org/wiki/Basic_access_authentication)之后，它将被标记为关闭。现在，您只需更新您的 Chill 配置，并提供您的用户名和密码，Chill 就可以通过 HTTP 基本身份验证开始监控您的网站。

**支持处于维护状态的**
Chill 现在知道网站处于维护状态。如果您的网站正在维护中，并且报告的 HTTP 状态为 503，并且`retryAfter`标头设置为大于零的值，则 Chill 会将其状态报告为正在维护中。当 web 应用程序和服务进行定期维护，并且不希望 Chill 报告停机时间时，这一点尤其有用。
*仪表盘页面上处于维护状态的集成还在后面。*

**服务重试支持**
为了确保网站关闭，我们现在尝试在发出通知之前多次获取网站的状态。这完全消除了我们在以前版本的 Chill 中看到的任何错误警报。服务重试的时间间隔也可以在 Chill 配置中更改。我们为那些不想改变时间间隔的人准备了一些默认设置。

**内部变化**

1.  我们所有的包和依赖项都是最新的版本。
2.  我们已经将所有的库合并成一个库。这使我们能够轻松地合作和做出改变。是的，[安装 Chill](https://github.com/leapfrogtechnology/chill/pull/55) 仍然很痛苦，但是我们很快就会让它变得比以前更容易。
3.  修复损坏的 Websocket 集成。
4.  移除 Hipchat 通知程序。[再见！](https://techcrunch.com/2018/07/26/atlassians-hipchat-and-stride-to-be-discontinued-with-slack-buying-up-the-ip/)👋

**未来**
我们还没完。我们希望为 Chill 添加更多功能，同时保持其核心。在接下来的几个月里，我们将继续稳步改变 Chill。一些令人兴奋的新功能正在变得越来越酷，包括 SSL 状态报告和 Slackbot 集成。关于这一点，我们想确保为每个版本写一篇更新博客。😎

> 在[https://github.com/leapfrogtechnology/chill/releases](https://github.com/leapfrogtechnology/chill/releases)与我们的发布保持同步

想为 Chill 做贡献吗？想给我们发送反馈吗？请随时联系 chill@lftechnology.com
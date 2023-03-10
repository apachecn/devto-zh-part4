# Web 应用程序中的脱机数据入门。一

> 原文：<https://dev.to/remotesynth/getting-started-with-offline-data-in-web-apps-pt-1-136a>

人们越来越期望现代网络应用程序能够以某种方式离线工作。事实上，离线可用性是 PWA 的一个关键部分。如果您的应用程序依赖于某种形式的数据(大多数都是这样)，这可能会很复杂。

在这一系列的文章中，我想看看离线处理数据的一些选择。其中的一个关键部分将是处理像[本地存储](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)和[索引数据库](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API)这样的东西。但是，确定是使用在线数据还是离线数据的一个重要步骤是了解您的应用程序当前是在线还是离线。在本系列的第一篇文章中，我将介绍一些非常简单的 web APIs，它们在这方面对您有所帮助。

## [T1】navigator . online](#navigatoronline)

[Navigator.onLine](https://developer.mozilla.org/en-US/docs/Web/API/NavigatorOnLine/onLine) 的目标非常基本——它只是将浏览器的在线状态返回为`true`或`false`。它和广告宣传的差不多。

[![navigator.onLine](img/b27e06b136a71003568f104e25b563aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LIbxLx8K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a4zavw8zvsjmlyvtp1pi.gif)

这将适用于除 Opera 之外的移动和桌面浏览器。

有两种方法可以利用这一点。第一个简单的条件句是:

```
if (navigator.onLine) {
    \\ call my external API for data
} 
```

Enter fullscreen mode Exit fullscreen mode

第二种方法是通过添加事件侦听器来响应用户在线状态的变化。

[![navigator.onLine](img/ce92026d943e069274ec54ec56ca0e76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N5kajt_y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/er9xpbw8w47uq2rmd8tv.gif)

## 网络信息 API

虽然`Navigator.onLine`运行良好，但除了用户是在线还是离线之外，它没有提供任何关于用户连接的细节。例如，如果用户的连接速度非常慢怎么办？在这种情况下，您可能希望首先依赖某种本地数据，当它可用时由远程数据刷新，或者根据远程数据的性质，甚至根本不需要远程调用。

理论上，这就是[网络信息 API](https://developer.mozilla.org/en-US/docs/Web/API/Network_Information_API) 所提供的——不仅仅是连接状态，还有关于连接的关键细节。那还有什么问题呢？它目前只能在 Chrome(桌面和 Android)和 Opera 上运行。

为了了解这是如何工作的，我创建了一个简单的代码笔。如果你在 Chrome 上，打开你的 Chrome 开发者工具到“网络”标签，然后改变节流下拉菜单(应该是“无节流”)到一个不同的预置，比如“快速 3G”、“慢速 3G”或者设置为“离线”

[https://codepen.io/remotesynth-the-bold/embed/rEpzWw?height=600&default-tab=js,result&embed-version=2](https://codepen.io/remotesynth-the-bold/embed/rEpzWw?height=600&default-tab=js,result&embed-version=2)

值得注意的一件有趣的事情是，当“离线”时，我的测试中的连接类型仍然是“4G ”,但是`rtt`和`downlink`都是零。这可能会让你问，这些值到底是什么意思？

*   `effectiveType` -连接类型为“慢速 2g”、“2g”、“3g”或“4g”四个值之一。
*   `rtt` -这代表“往返时间”这是“数据包从发送端点到接收端点并返回所用的时间”([来源](https://www.callstats.io/blog/what-is-round-trip-time-and-how-does-it-relate-to-network-latency))
*   `downlink` -该值是带宽的估计值，单位为每秒兆比特。
*   `saveData` -该值表示用户是否启用了某种减少数据使用的选项。

如果网络信息 API 被更广泛地采用，那么它可能有助于确定何时依赖远程数据还是本地数据。

## 下一步

在这篇文章中，我们看了一些工具，以确定用户的互联网连接是否允许我们可靠地从远程数据源获取数据，而不是本地数据。在本系列的下一篇文章中，我将开始研究使用 localStorage 在本地存储数据的一些方法，然后在后续的文章中，介绍 IndexedDb 和一些有用的工具。
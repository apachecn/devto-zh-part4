# 。开发域无法解析

> 原文：<https://dev.to/brandonweiss/dev-domains-not-resolving-273b>

在过去的一年里，我无法在我的电脑上查看任何`.dev`域名。这个问题很简单，但是我花了*长*的时间才弄明白它是什么。

一旦谷歌开始在`.dev`<abbr title="Top-level domain">【TLD】</abbr>出售域名，开发者们就开始抢购域名，并将其用于他们的网站、博客和其他项目。

奇怪的是，我发现我一个也看不见。

每当我点击进入一个`.dev`域名的网站时，它都无法解析，我的浏览器出现一个空白的白屏。

起初，我把它归因于网站关闭或我的 <abbr title="Internet service provider">ISP</abbr> 的问题，并忽略了它，但在一个月内无法跟踪十几个链接到`.dev`域后，最终它变得很清楚，这个问题是我这边的事情。

我念了一些我能想到的常见咒语。我试了试 [`dig`](https://en.wikipedia.org/wiki/Dig_(command)) ，但是好像没什么问题。我尝试了 [`ping`](https://en.wikipedia.org/wiki/Ping_(networking_utility)) 和 [`traceroute`](https://en.wikipedia.org/wiki/Traceroute) ，这两个命令都挂起，这表明有问题，但也没有返回任何我可以用来缩小问题范围的信息。

我在网上四处搜索，但找不到任何有类似问题的人，于是放弃了。每次我链接到一个`.dev`域名时，我都会重复这个过程，花几分钟尝试不同的搜索。最后，将近一年后，我发现了一个不相关的问题，让我想到我的问题可能是什么。

问题是 [Pow](http://pow.cx) 。没错，大约六年前的时髦开发服务器。

您可能仍在使用 Pow，但更有可能的是，您已经多年没有使用它，并且完全忘记了它。我当然有。

不幸的是，我没有正确地卸载 Pow，它的解析器仍然悬而未决，将所有对`.dev`和`.test`的请求路由到一个黑洞中。

```
❯ ls /etc/resolver/
dev  test 
```

Enter fullscreen mode Exit fullscreen mode

```
❯ sudo rm /etc/resolver/dev
❯ sudo rm /etc/resolver/test 
```

Enter fullscreen mode Exit fullscreen mode

删除解析文件，你应该可以去✌了🏼
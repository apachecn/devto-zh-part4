# 这是一个价值 100 万欧元的节点脚本吗？

> 原文：<https://dev.to/cobe_tech/is-this-1-000-00-worth-node-script-548l>

所以故事从我最喜欢的 Youtube 频道之一开始。几天前，他们发布了一个新的视频，宣布他们将很快达到其 10 万订户，届时将发放一张**100 万欧元**的代金券，该人可以用它购买新设备。
那可是一大笔钱！尤其是如果你像我一样，住在克罗地亚。我们在这里谈论的是比平均月工资多 20%左右。

## Plot

我必须承认，听到这个消息后，我立刻就被吸引住了。我暂停了视频，开始想办法成为 10 万订户并赢得代金券。接下来我明白了，手动检查订户数量并等待合适的时机点击订阅按钮不会给我带来任何运气。如果我想比别人更快，我需要一台机器来帮我做这项工作。

[![Fast as machine](img/fcd13bc38d534e9a733166c2e051d09c.png)](https://i.giphy.com/media/RRerwvHrb0nxm/source.gif)

经过短暂的头脑风暴，我想出了一个简单的解决方案，我唯一需要的就是 [Youtube API](https://developers.google.com/youtube/v3) 和一点 Node。解决方案非常简单:我们必须知道该频道有多少订户，所以当它达到 99.999 订户时，我们就需要订阅。

## 挑战

如果您已经在考虑实现，那么您知道我们将需要 [`setInterval`](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setInterval) 或 [`setTimeout`](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout) ，以便我们可以反复检查订户的数量。

这里我们唯一的限制是 [YouTube 数据 API 配额使用](https://developers.google.com/youtube/v3/getting-started#quota)。我们每天获得 10.000 单位的默认配额分配，这意味着每个请求将花费我们一些单位。

在我们的例子中，如果我们想要获得一些频道用户，我们需要向`https://www.googleapis.com/youtube/v3/channels?part=statistics`发出请求，这个请求将花费我们 3 个单位。你可以使用 [Youtube 配额计算器](https://developers.google.com/youtube/v3/determine_quota_cost)计算你的配额成本。

如果我们每秒都检查订阅者的数量，我们将通过运行 55 分钟的脚本来耗尽我们的配额，这是远远不够的；我们希望我们的脚本运行更长时间，这样我们就不会错过我们的机会。这种限制的唯一解决方案是根据用户数量使用不同的间隔延迟。这意味着当订阅者数量接近 100k 时，我们将更频繁地执行请求。在开始时，我们将从每小时一个请求开始，然后我们将一直到 300 毫秒。

## 解

检查以下回购中的解决方案，并尝试一下。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [达芬奇 2015](https://github.com/davinci2015)/[YouTube-订户](https://github.com/davinci2015/youtube-subscriber)

<article class="markdown-body entry-content container-lg" itemprop="text">

# Youtube 用户

如果你想知道为什么要创建这个回购，请阅读[这个故事](https://dev.to/cobe_tech/is-this-1-000-00-worth-node-script-548l)关于 100 万欧元的凭证。

## 装置

克隆存储库

```
$ git clone https://github.com/davinci2015/youtube-subscriber.git
```

Enter fullscreen mode Exit fullscreen mode

cd 进入目录

```
$ cd youtube-subscriber
```

Enter fullscreen mode Exit fullscreen mode

安装 NPM 依赖项

```
$ npm install
```

Enter fullscreen mode Exit fullscreen mode

### 配置

在运行脚本之前，您应该更新`config.js`文件中的变量。

更新以下变量:

*   `channelId`
*   `client_secret`
*   `client_id`

### 运行服务器

更新配置变量后，你可以运行服务器，让他变魔术。

```
$ node main.js
```

Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/davinci2015/youtube-subscriber)

## 扭动

在我完成了实现之后，我继续看他们宣布他们的赏金的视频。在那一刻，我知道我搞砸了。这从来都不是关于成为第 100 个订户，而是关于访问他们的社交档案并参与随机抽取获胜者的有奖游戏。

所以我来了，带着出版的剧本，却没有 100 万欧元的代金券。但是让我们看看光明的一面——至少我学到了一些新的东西，也许在未来，这个脚本可以帮助一些人赢得代金券，谁知道呢。
到那时，编码快乐！
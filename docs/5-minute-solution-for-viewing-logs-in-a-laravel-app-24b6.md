# 在 Laravel 应用程序中查看日志的 5 分钟解决方案

> 原文：<https://dev.to/adi/5-minute-solution-for-viewing-logs-in-a-laravel-app-24b6>

### 简介

大家好，距离我上一篇与 Laravel 相关的帖子已经过去几个月了。所以我决定写一些小东西来回到时间表中。我计划每个月甚至更频繁地发布一些东西。

我们都遇到过这种情况，你的 Laravel 应用程序不能像预期的那样工作，你想尽可能快地获得日志。在大多数情况下，我们可能不容易访问日志文件，因为我们需要一个 ssh 客户端来连接到服务器，然后导航到日志，最后查看其中的最后一个条目。做这件事总是很麻烦。在这篇文章中，我想向你展示我在应用程序中查看日志的简单解决方案。我在最近的一个客户项目中实现了这一点，效果很好。

一如既往，如果你有更好的实现，请分享，我很想知道它。

### TLDR；

这是我的解决方案的概述。我有一个到页面的路由，该页面的控制器读取当天的日志文件并发送给视图。该视图有两个部分，首先，它有一个表单来更改当前查看的文件的日期，其次是显示日志的部分。

很简单是啊。如果不够清楚，请从下面检查代码。

### 代码

首先，在你想要显示日志文件条目的地方添加一条路线
[![Route file content](img/b8c55f9cc84930204ac75abf2037b27f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TzLgWgnj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/hZ22Hcy.png)

然后是控制器，下面是控制器的逻辑。这里我们得到了需要日志的日期。然后，我们提取关于文件及其内容的信息，并将其发送给视图。

[![Controller file contents](img/2e2ec4d2f73edba3d55bd21e3561e460.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ESKrFc_3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/TDQp1QZ.png)

在这个视图中，我们有两个部分，一个是更改日志文件的日期，另一个是查看实际日志文件的内容。
[![View file contents](img/ca9b801b48890c7c83fc78d0c5e36ccf.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--B-GGAFpP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/AgfUdSi.png)

一旦你把所有的东西放在一起，你就有了自己的日志浏览器。下图是我加了一些 CSS 之后的样子。对于 5 分钟的工作来说还不错。
[![Final result](img/93c7d38bad38b98136e95a890c67216c.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--_cy2mvLU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/UhphURe.png)

### 结论

我希望这个解决方案对你有用。如果你有更好的解决方案，一定要让他们知道。我非常乐意尝试一下。

**相关资源**

*   [https://laravel.com/docs/6.0/errors](https://laravel.com/docs/6.0/errors)
*   [https://github.com/rap2hpoutre/laravel-log-viewer](https://github.com/rap2hpoutre/laravel-log-viewer)
*   [https://github.com/ARCANEDEV/LogViewer](https://github.com/ARCANEDEV/LogViewer)

目前就这些。下次再聊。
在[simples web . in](https://simplestweb.in/?utm_campaign=LCJobs-launch-article)了解更多关于我的信息
# 从 CommonCrawl 存档中删除网页？

> 原文：<https://dev.to/wtg/scrapping-webpages-from-commoncrawl-archive-3kfm>

我最近启动了一个开源项目，为 CommonCrawl web archive 构建一个 Nodejs scrapper。

当前版本为任何给定的 URL 获取快照，下一个版本将解析这些快照并给出 RAW html 数据输出。从那里，Cheerio 可以用来解析单个数据点。

它帮助抓取网页的速度比从他们自己的主机服务器抓取网页快 100 倍，而且数据是最新的。

CommonCrawl 数据托管在 AWS 上，从同一个具有 Lambda 函数的 DC 运行(我有更高的限制),我能够在大约一天内抓取和解析 3 亿个页面，而不用担心速率限制、僵尸检测、代理或降低网站速度。

这是你喜欢看到的吗？[感兴趣的话链接到回购](https://github.com/subhashchy/commoncrawl)
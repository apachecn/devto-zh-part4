# 通过投票订购工程博客条目

> 原文：<https://dev.to/juanpabloaj/ordering-engineering-blogs-entries-by-votes-3ep0>

TL；速度三角形定位法(dead reckoning)

订购工程博客列表中包含的 RSS 条目的网站

[https://github.com/kilimchoi/engineering-blogs](https://github.com/kilimchoi/engineering-blogs)

这是网络

[https://entries-by-votes.now.sh/](https://entries-by-votes.now.sh/)

源代码

[https://github.com/juanpabloaj/entries-by-votes](https://github.com/juanpabloaj/entries-by-votes)

## 简短描述

如果你像我一样，可能作为你日常工作的一部分，你会查看一些工程博客。几个月前，我发现了这个回购与一个惊人的博客列表。

[https://github.com/kilimchoi/engineering-blogs](https://github.com/kilimchoi/engineering-blogs)

它很棒，但是如何识别最有趣的(或最受欢迎的)帖子呢？嗯，我记得这篇文章是关于黑客新闻是如何排列他的帖子的。

[https://medium . com/hacking-and-gonzo/how-hacker-news-ranking-algorithm-works-1 d9 b 0 cf 2c 08d](https://medium.com/hacking-and-gonzo/how-hacker-news-ranking-algorithm-works-1d9b0cf2c08d)

```
Score = (P-1) / (T+2)^G

where,
P = points of an item (and -1 is to negate submitters vote)
T = time since submission (in hours)
G = Gravity, defaults to 1.8 in news.arc 
```

有了这些信息，几行 python/tornado 从 Hacker News 和 Reddit 获得的投票，以及我们的朋友们[zeit.co/now](https://zeit.co/now)(作为无服务器部署服务)的帮助，晚餐已经准备好了。

和平。
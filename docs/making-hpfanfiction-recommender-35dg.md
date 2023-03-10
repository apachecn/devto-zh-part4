# 制作 HPFanfiction 推荐器

> 原文：<https://dev.to/haideralipunjabi/making-hpfanfiction-recommender-35dg>

## 背景

自 2017 年底以来，我一直在阅读哈利波特同人小说。大约在 2018 年年中，寻找更多的故事变得非常困难，我开始在 [HPFanfiction 子编辑](https://reddit.com/r/HPFanfiction)中寻找故事建议。几个月后，[的 subreddit](https://www.reddit.com/r/HPfanfiction/comments/9pw53f/a_lot_of_people_liked_the_fic_recommender_i/) 上发布了一个 fic 推荐工具。这是基于一些以前的研究&数据(我会在文章末尾链接到所有相关的东西)。它基本上是通过比较喜欢两个不同的 fic 的用户并生成相似性分数来工作的。这个工具(和它的制造者 ElusiveGuy)是我的工具背后的巨大灵感，我最初希望使用相同的方法，但使用更大的数据库(我对 web 抓取艺术略有涉猎)。

## 初步计划

正如我所说，我最初的计划(大约在 2018 年 12 月)是收集比 ElusiveGuy 的工具正在使用的数据多得多的数据。我知道 fanfiction.net 不喜欢抓取脚本，但我只想要故事的元数据，而不是故事本身。我还计划每 2-3 秒发出一次请求，这样他们的服务器就不会遇到任何问题。

这个计划的问题是，没有办法获得喜欢某个特定故事的用户列表。你只能得到一个特定用户喜欢的故事( [vir_innominatus](https://www.reddit.com/user/vir_innominatus/) ，收集数据的人有一个他以前使用的分析中的用户列表)。我现在也许能够完成这个，因为我现在也有一个庞大的作者名单(7.5k +)。

我不得不放弃这个计划，搁置这个项目，直到灵感迸发。

## 直爽中带直爽，弯曲中带弯曲

2019 年 7 月，当我在研究 Reddit 机器人时，灵感迸发，具体来说就是 [reddit-ffn-bot](https://github.com/tusing/reddit-ffn-bot) 。这让我想到了解决这个问题的完全不同的方法。我知道我不能完全打动 fanfiction.net，但我可以打动 Reddit。我的新想法是抓取 [HPFanfiction subreddit](https://reddit.com/r/HPFanfiction) 并收集人们推荐的数据。我知道这个想法有价值，但我必须学会如何恰当地利用 Reddit。

## [的权利](#praw)

我见过许多项目使用 PRAW 与 Reddit 合作。我也想过做同样的事情，看起来相当简单。在用 PRAW 做了一个基本的刮刀后，我遇到了一个大问题。我只能访问 subreddit 中的 1000 个帖子。它会创建一个比我希望的更小的数据库。是的，它会随着时间的推移而增长，但我不喜欢它。我保留了 PRAW 的方法作为备用。我知道 Reddit 上有很多庞大的数据库，但是要过滤出某个特定用户的评论是很困难的。在我的搜索中，我发现了 Reddit 数据的金矿。

## pushshift.io

在浏览互联网一段时间后，我发现了 [pushshift.io](https://pushshift.io) ，它显示了各种与 Reddit 相关的统计数据，以及一个包含大量 Reddit 数据的伟大 API。API 可以给我从 2015 年 6 月以来来自[u/fanficationbot](https://www.reddit.com/user/FanfictionBot/)的分类评论。4 年的数据。这是我一直在寻找的。这个 API 对于各种 Reddit 相关的项目都有很大的潜力。我期待着将来也能使用它。

## 初始刮削

第一步是在我自己的电脑上建立一个基础数据库，然后使用服务器定期处理少量数据。我没有世界上最稳定的互联网，我也意识到在抓取如此大量的数据时脚本可能会崩溃。我制作了另一个小脚本来下载所有的数据，这样我就可以在本地文件上工作。我最初下载的是 46 个 json 文件，大约 120 兆字节的数据。我花了一段时间来编写一个抓取算法，它可以处理这些年来所有的模板变化。我最初只关注故事标题、故事 URL、作者姓名和作者链接。我的电脑花了大约 3 个小时收集了 7 万多个故事。创建数据库后，我编写了一些代码来执行给定的 SQL 命令，并将其结果导出到 JSON 文件中。这些 JSON 文件将被前端使用。

最初，我创建了以下 JSON 文件:

*   最新 1000 个故事
*   前 100 个故事(过去 7 天、过去 30 天、过去 365 天和有史以来)
*   前 100 名作者(过去 7 天、过去 30 天、过去 365 天和有史以来)

## 制作前端

选择正确的方式来制作前端是一个相当困难的选择。在选定最简单的 HTML/CSS + JS/jQuery 之前，我尝试了很多东西(React、Vue、Hugo 等等)。我知道我不需要很多元素，但是需要大量的代码。比起 React，我更习惯使用 jQuery。所以，做出选择后(4-5 个小时)，我开始做前端。用[布尔玛 CSS 框架](http://bulma.io)制作网站花了我大约 1.5 小时。所有的[前端代码在 Github](https://github.com/HackeSta/hpffrec-website) 上都有。

## 链接后端&前端

这大概是很多人面临问题的地方。我自己的方法有些非正统，因为我还没有见过其他人这样做。我自己已经做了 4-5 年了。有点像伪 JAMStack 的东西。

我在  上托管了我的脚本。我正在使用它的免费计划，它已经足够我所有的项目。我已经设置了一个更新数据库的 CRON 作业(在自由计划中每天 1 个)。它产生了我前面提到的 JSON 文件集，还触发了前端的 Travis CI 构建。

TRAVIS CI 构建从服务器下载 JSON 文件，并将它们提交给 repo。这样，后端每天只被访问几次(完全在免费计划的范围内)，前端(Netlify 托管的)处理所有的流量(这是我的项目中最大的流量)。

在未来，我计划每天增加 CRON 作业，要么通过向 PA 请求它们，要么通过我在其他项目中使用的另一个技巧。

## 首次发布后更新

Reddit 上的[初始发布帖子获得了大量的浏览量和大量的改进建议。](https://www.reddit.com/r/HPfanfiction/comments/camzsb/hpfanfiction_recommender_tool_website/)

添加元数据(故事长度、章节、流派、评级等)是其中最大的一项。由于模板多年来不断变化，为元数据编写一个抓取算法被证明是极其困难的。我最终决定只抓取较新的模板，并抓取大约 56%的故事的元数据。这是一个很好的解决方案，因为所有没有得到元数据的故事在很长一段时间内都不会被[u/fanficationbot](https://www.reddit.com/user/FanfictionBot/)链接。

添加元数据后，下一个明显的更新是添加基于元数据的过滤器。最初，我计划为每个过滤器组合创建单独的 JSON 文件，但是它们的大小太大了。我被迫过滤前端已经在使用的数据，但是我将故事和作者的数量从 100 增加到 1000 以进行补偿。效果非常好。

还进行了较小的更新，包括:

*   添加选项以更改页面中显示的条目数
*   没有找到数据时添加进度条和消息

## 决定 CRON 工作的最佳时间

为了决定 CRON 作业启动的最佳时间，我做了一些分析并生成了下面的图表。

[IMGUR 图表集](https://imgur.com/a/QSzBR8w)

如您所见，UTC 17:00 是 CRON 作业的最佳时间，我目前正在使用它。我正计划使用我知道的另一个绝妙的技巧来增加 CRON 作业的每日数量

## 我面临的随机问题

1.  对旧版本字体 Awesome 的请求太多:我在我的项目中添加了字体 Awesome 来添加元数据图标，但它大大降低了网站的速度。[这个 Github 问题](https://github.com/FortAwesome/Font-Awesome/issues/15167)告诉我这是他们那边的问题，我希望他们尽快解决。
2.  **未出现的成对字符:**[u/fanficationbot](https://www.reddit.com/user/FanfictionBot/)模板是这样的，成对字符列在'< ' & ' >'中。这使得前端 HTML 将它们视为标记名，并创建了一个元素。为了解决它，我采用了以下方式:

    `.replace(/</g, '&lt;').replace(/>/g, '&gt;')`

## 参考文献

*   [洗脱维吉的工具](https://ficrecs.elusiveguy.com/)
*   [原始数据的 Reddit 帖子](https://www.reddit.com/r/HPfanfiction/comments/9nc0cw/ever_thought_i_wish_i_could_find_more_fics_like_x/)
*   [同人小说、图表和页面排名](http://colah.github.io/posts/2014-07-FFN-Graphs-Vis/)
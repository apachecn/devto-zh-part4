# 刮 NBA p1-球员/球队

> 原文：<https://dev.to/loganwohlers/scraping-the-nba-p1-players-teams-5k5>

作为一个附带项目-我一直在使用 basketballreference.com 的免费统计数据构建一个 NBA API。最终，我的目标是为任何想使用篮球统计数据的人提供一个简单的 API，这样就没有人必须经历我所经历的一切——为什么 NBA 不提供免费的 JSON 数据超出了我的理解范围——但这就是这个项目的由来。当前最好的选择是 balldontlie.io，它很好，但是没有提供我设想的 API 应该有的所有统计数据。因此，为了补救这一点，我一直在收集大量数据，并将其保存到我自己的个人数据库中，该数据库将托管在某个有记录的端点的地方。这个项目很快就要发布了——我已经完成了清理过程，我想我会在这里进行扩展。我在 Rails 中使用 Nokogiri gem 开始了这个项目，但后来改用 Node 和 Cheerio/Puppeteer 来做抓取工作——过程基本上是相同的，但因为我最近更喜欢 JS，所以我将从这个角度出发。所以，废话不多说，这是这个系列的第一部分-球员和球队。

让我们从团队开始，因为我将在后面的帖子中更详细地介绍他们。目前，NBA 的 30 支球队(RIP SONICS)被硬编码在一个静态 JSON 文件中，每支球队都有一个对象，其中包含球队的名称、城市、联盟和三码(即 LAL 代表洛杉矶湖人队，ATL 代表亚特兰大老鹰队，等等)。在数据库中有一个相应的表包含这些信息——所以每当团队数据需要在流程中播种时，就像运行这个文件并为每个团队创建行一样简单。在我目前的建设中，球队也有他们的平均数据以及他们的对手的季度数据——可以在这个网址([https://www.basketball-reference.com/leagues/NBA_2019.html](https://www.basketball-reference.com/leagues/NBA_2019.html))找到。这将在以后的文章中详述，但是现在一个简单的球队列表对于我们的球员来说已经足够了。

现在开始实际搜集球员数据。当前的数据库是这样建立的，玩家是他们自己的实体——也就是说，他们不属于一个球队，而是一个玩家玩属于一个球队和一个赛季的 player_seasons。对于每个赛季，basketball reference 都提供了一个表格，其中包含 NBA 名册上每个赛季结束的球员及其赛季平均水平(即[https://www . basketball-reference . com/league/NBA _ 2019 _ per _ game . html](https://www.basketball-reference.com/leagues/NBA_2019_per_game.html))。快速浏览一下页面，就会发现该牌桌的 id 为#per_game_stats。所以使用任何抓取方法，我们首先加载这个 url，然后直接进入这个表。

所有实际的玩家信息都包含在主体中——所以我们进入主体，搜索所有的表行，并开始使用类似 for 循环的方法遍历它们来获取数据。对于正文中的每一行，我们必须遍历所有的 td 单元格并获取它们的数据。我只是创建了一个空数组，然后为每一行的 td 映射出 stat 名称和它们的值，并将它们放入一个对象中。统计信息的名称实际上是在 td 中作为一个名为 data-stat 的属性提供的，它允许您放弃使用表头列名，直接从主体中获取所有相关数据。下面是这段简单代码的一部分。

```
 let result=[]
 const tableBody = $('#per_game_stats').children('tbody')
    tableBody.find('tr').each((index, ele) => {
        let row = {}
        $(ele).find('td').each((index, ele) => {
            let statName = $(ele).data().stat
            let statVal = $(ele).text()
            row[statName] = statVal
        })
        result.push(row)
    })
    return result 
```

Enter fullscreen mode Exit fullscreen mode

所有这些都设置好了——要实际植入数据库，我只需为球员的名字(行中的第一个值)找到或创建一个新行，使用他们的 tri-code 找到预先植入的球队的 id，并创建一个引用所述球员和球队的新球员赛季行。这个过程实际上非常快，因为这个表中每个赛季只有大约 600-800 名球员。

下周，我将更深入地探讨更难的部分——以一个赛季为例，为每场比赛播下一个 boxscore(一个赛季 1230 分)。敬请关注。

感谢阅读，并让我知道任何问题/评论！

洛根
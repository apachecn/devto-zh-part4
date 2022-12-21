# 用 R 分析英雄联盟数据

> 原文：<https://dev.to/couch/analyzing-league-of-legends-data-with-r-2kcc>

## 采集数据

如果你想从公开比赛中分析所有游戏玩法，Riot 有一个很棒的 [API](https://developer.riotgames.com/) 。对我来说，我喜欢看竞技比赛中的职业选手；一个极好的资源是[甲骨文的仙丹](http://oracleselixir.com/)。具体来说，我使用匹配数据文件来开始我的所有研究。

## R 部分

正如我所说，我们将使用由[甲骨文公司的 Elixer](http://oracleselixir.com/match-data/) 提供的比赛数据文件，我们将使用 2019 年夏天的文件。如果你还不熟悉 R，没关系！今天我们就简单点。你所需要的只是开始 [R 工作室](https://www.rstudio.com/products/rstudio/download/)。现在我们将使用两个库:`Tidyverse`(一组用于数据操作的库)和`openxlsx`(您可能已经注意到文件格式是 excel 文档，这让我们可以轻松地打开它)。有了这两个库，我们就可以开始了！
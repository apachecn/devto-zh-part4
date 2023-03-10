# 德克萨斯州奥斯汀的住房负担能力

> 原文：<https://dev.to/rpost/housing-affordability-in-austin-tx-1ne2>

# 动机

我住在奥斯汀的一个街区，那里正在进行大量的翻新和建设。我从个人经验中知道，城市许可部门有一个在线搜索-我们也做了翻新。我意识到他们也有一个公共 API，所以我决定探索是否可以绘制我所在地区的有效建筑许可，并更好地了解社区正在发生的变化。

# 形势

原来城市的数据集是一个惊人丰富的数据来源！我很快就看到了我对自己的邻居感兴趣的东西，并意识到在这个地区也有影响。许多消息来源会告诉你，奥斯丁是这个国家发展最快的大都市地区之一( [1](http://austin.culturemap.com/news/city-life/02-26-19-austin-area-population-in-2019-growth-rate-reports-demographer/) 、 [2](https://www.opendatanetwork.com/entity/310M200US12420/Austin_Metro_Area_TX/demographics.population.count?year=2017) 、 [3](https://www.bizjournals.com/austin/news/2019/04/18/as-some-big-cities-lose-residents-austin-is-adding.html) 等等)。)这种增长的一个不利方面是，随着更多的人进来并增加需求，住房变得越来越难以负担。许多音乐家，作为“世界现场音乐之都”的重要组成部分，被迫和 5 所高等教育机构的学生一起搬出市区，甚至许多双职工家庭也无法负担住在市区内的费用。更远的生活有更低的住房成本，但需要更多的交通时间，造成更多的拥堵，并占用了我们大多数人宁愿花在其他任何事情上的时间。

这座城市意识到了负担能力的问题，并且很清楚事情的现状。房价中值和总租金中值是衡量经济承受能力的两个关键指标，这两个指标越来越差。这两者都受到现有住房单元数量的影响。如果住房成本要下降，人们想要居住的地方就需要有更多的单元。在基地，它的供应和需求-当我们没有足够的单位，业主和卖家可以收取更多。当我们有很多的时候，他们不得不收取更少的费用。

# dev 连接

“等等，等等，”你说，“好吧，我可以看出这是**一个东西**，但是作为一个开发人员，我为什么要关心呢？”

首先，我希望你在乎，因为你不喜欢看到其他人挣扎着去挣足够的食物和住房。

第二，奥斯汀是技术工作的热门地点，我们相对较高的工资帮助推高了房价。这里的家庭收入中值[根据一些估计，略低于 66K，](https://austin.curbed.com/2019/4/15/18311751/austin-rent-median-affordable-wages-salaries-burdened)比许多技术人员的工资还低。虽然我们可以处理生活成本，但我们正在拉高工资中位数，使其他人更难生活。我认为那并不坏——我喜欢挣一份体面的薪水！我确实认为，我们应该意识到我们这个领域的影响，并不是每个人都能挣到类似的工资。

第三，我们知道如何使用 API，因此我们可以利用可用的数据做一些事情。我们中那些能够以可视化的方式呈现数字并将其分解的人可以帮助理解代表一个相当复杂的问题的数字。

# 提问

这给我们带来了一个可衡量的问题:

> 如果我们将城市中正在建造的住房单元的数量与迁入该地区的人口数量进行比较，有多少新来者能够在城市中找到一个地方，又有多少人会与来自周边地区的交通发生冲突？

换句话说，我们有机会很快解决这个负担能力问题吗？

# 计划

为了确定新人口与新住房的比例是否有利于降低住房成本，我们需要确定搬到该地区的新人口的预期数量，以及我们对新住房单元的预期。人口估计很容易找到，所以我将研究住房。

在此之前，请随意查看截至 2019 年 8 月 20 日德克萨斯州奥斯汀市的有效居住许可地图:[https://public.tableau.com/profile/rebekah3261#!/viz home/new units/ActivePermitUnits](https://public.tableau.com/profile/rebekah3261#!/vizhome/NewUnits/ActivePermitUnits)
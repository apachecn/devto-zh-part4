# 将 Sandeep Lamichhane 的 Twenty 20 stats 与其他著名美腿进行比较

> 原文：<https://dev.to/saral/comparing-sandeep-lamichhane-s-twenty-20-stats-with-other-prominent-leggies-5c1l>

“Sandeep Lamichhane”这个名字当然不需要介绍。他通过在 IPL、Big Bash 和其他国际 T20 联赛中的出色表现，在 Twenty 20 板球界掀起了波澜。然而，我想看看桑迪普与其他一些传奇人物和他同时代的人相比到底表现如何。

摆弄[仪表盘](https://cricketapp-spinner.herokuapp.com/)

所有的数据都是从 Cricinfo.com 收集的，在对数据进行细致的清理后，以下是调查结果。名单中的保龄球手是:沙恩·沃恩、阿尼尔·昆布尔、拉什德·卡汉、尤兹文德拉·查哈尔、阿米特·米什拉、伊姆兰·塔希尔、亚当·赞帕、阿迪勒·拉希德、拉什德·卡汉和桑迪普·拉米查内。

### 比赛场次:

<figure>[![](img/2680b2c629cf4ebdfaa7677378900d5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4-CDFDLW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3GtWfhitsfb1nuYB3XH7qA.png) 

<figcaption>比赛总次数</figcaption>

</figure>

相对而言，Sandeep 对于 T20 联赛来说是一个非常新的领域，与其他人相比，他参加的比赛要少得多。阿尼尔·昆布尔和沙恩·沃恩是名单中仅有的两名前球员，但即使是他们也比桑迪普打得多。不过，桑迪普可能会赶上这两位传奇人物。

在 T20 板球比赛中，判断投球手能力的一些关键指标是

*   经济舱率:板球比赛中每回合失球的平均次数。
*   保龄球击球率:测量投球手每次击球的平均投球数。
*   Average:是他们在每一次击球中失分的次数。

然而，人们普遍认识到，这种统计数据在评估运动员的真实能力方面有严重的局限性[1]。因此，我们将关注由 Lemmer 开发的称为组合保龄球率(CBR)的测量。[2] CBR 是通过使用 A、E 和 s 的调和平均值构建的。出于计算目的，它可以写成:[3]

<figure>[![](img/cedb8fb604398f96898f5e365a143282.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TO6CYgO9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/264/1%2AiGUA5W5y259t2ekE4XM-Cw.png) 

<figcaption>CBR 公式</figcaption>

</figure>

我们将面临的一个限制是，如果投球的次数很少，这个值是可变的。然而，在我们的例子中，所有的投球手都投了足够多的圈数，我们可能不需要担心少数圈数的限制。

<figure>[![](img/75be0165e2c720eb66849ea61edb0fff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nDFItULC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/196/1%2AYP4xDN7Qzk5I9QvmLkLV2Q.png) 

<figcaption>总投球数</figcaption>

</figure>

使用 CBR，我们可以很好地了解 Sandeep 的表现。

### CBR Vs 经济:

<figure>[![](img/e5fbc9f847613a0feb0c9558369d0859.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tfygkYmp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A46_BVwy-yLqujFInAzh-uw.jpeg) 

<figcaption>CBR vs 经济率</figcaption>

</figure>

我们可以看到，根据经济状况，Sandeep 在榜单上排名第四。然而，如果我们看一下 CBR，根据前面引用的作品，CBR 是更好的性能衡量标准，我们可以看到，Sandeep 跃升至第三位，仅次于拉什德·卡汉和 Imran Tahir。

从 CBR 得分和经济率来看，我们可以看到 Sandeep 在他短暂的 T20 投球手生涯中是成功的。

令人惊讶的发现:看到沙恩·沃恩在这份名单中垫底是相当有趣的。不过也有一个例子可以证明他:这位冠军选手处于职业生涯的后期，因此并没有处于最佳状态。

对于[完整代码](https://github.com/Saralkarki/bowling_performance/blob/master/Project.ipynb)

—
参考资料:

1.  在为期一天的板球比赛中对运动员表现进行更公平的衡量。_ 运筹学学会杂志。_2005，**56**:804–815。
2.  板球运动中作为保龄球表现衡量的综合保龄球率。南非运动、体育和娱乐研究杂志。 2002 年，**24**(2):37–44。
3.  在板球中使用组合保龄球率的观点。*国际体育科学杂志&教练第 9 卷第 3 期* 2014，513–523
4.  Bhattacharjee D，Pahinkar D . G .,*使用综合保龄球率分析保龄球员的表现，* **《国际体育科学与工程杂志》**第 06 卷(2012 年)№03，第 184–192 页
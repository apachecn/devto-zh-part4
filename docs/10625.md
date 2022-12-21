# 爱丁堡科特林用户组的规划和成立会议

> 原文：<https://dev.to/rossdrew/planning-and-inaugural-meeting-of-the-edinburgh-kotlin-user-group-3n04>

注:改编自我在[交响乐软件](https://www.symphonicsoft.com/technology-choices/)的博文

## 我们为什么要成立一个 Kotlin 用户群？

我的公司最近开始从 Java 代码转向 Kotlin。我们从来不羞于技术改造。我们从一个完整的。NET 堆栈，通过。NET/Java 实现然后到 Angular/Java。在尝试 Elm、Ember、JavaScript 和 REACT 的过程中，我们学到了一些 Groovy 和 Python。我们甚至邀请了我们的高级开发人员之一 Neil Gall 在 2018 年欧洲 python 大会上展示我们的 Python 测试方法。对我们来说，与时俱进并使用最好的工具来创造最好的产品非常重要。

尽管我们在 [Symphonic](https://www.symphonicsoft.com/) 组建的团队已经以技巧和热情适应了不断变化的技术，但我们发现传统不会很快扎根。至少在使用新语言的一段时间里，我们发现自己在制造债务。我们编写了非 Python 的 Python 和 bent Groovy 来做我们当时不知道如何用它来表达的事情。
这是因为熟悉一门语言的复杂性需要时间和经验。我们可以通过与使用这种语言的人交谈和互动来增加这种接触，也许可以减少时间，所以我们决定经营一个用户组。

## 我们是怎么做到的？

*   制定一份预算，列出我们希望花费的费用，以使它取得成功。
*   整理一些海报，我们可以贴在城市的科技网站上
*   在 [Reddit](https://www.reddit.com/r/Edinburgh/comments/bhlcbk/edinburgh_kotlin_meetup/) 、[爱丁堡科技日历](https://opentechcalendar.co.uk/group/564-edinburgh-kotlin-user-group)、 [Twitter](https://twitter.com/edi_kug) 和 [EventBrite](https://www.eventbrite.co.uk/o/ross-drew-23262607307) 上做广告
*   购买的茶点(啤酒、果汁、比萨饼)
*   决定了地点(我们的办公室)并组织了一个演讲人(由我们的工程主管 Ewan Dawson 做的“面向 Java 开发人员的 Kotlin”)
*   在科特林社区注册并通知他们即将到来的演讲，他们为我们提供了免费的 t 恤和贴纸
*   向[开发者社区支持](https://www.jetbrains.com/community/support/#section=communities)注册，他们为我们提供了一年免费的 JetBrains 许可证来抽奖。
*   写了一个[小 Kotlin 程序](https://github.com/rossdrew/programmingproblems/blob/1a575a1a25b1a319be7ae9fe0a34eebbd6fa1ebb/src/main/kotlin/com/kug/Tumbola.kt)，包含了一个有趣的语言怪癖，它选择了一个在场的人来获奖。

```
import kotlin.random.Random.Default.nextInt

/**  * Number of people playing  *  * Q: Anyone know why val requires to be declared  *    const yet 'lateinit val T = 9' indicates  *    that val is immutable?  */
const val PLAYERS = 10

/**  * Original: Pick a winning number from 1 to players  */
fun pickWinningNumber(players: Int = 1) : Int {
    return nextInt(1, players)
} 
```

## 怎么样了？

如果我随后发出的 SurveyMonkey 调查是可靠的话，11 名与会者玩得很开心。人们登录后，我将该列表用作消防法规的健康和安全列表，并作为发送我的调查的邮件列表的开头。

这个旨在为 JetBrains 许可证挑选获胜者的小程序变成了一个话题。首先是因为它引起了有趣的 IDE 抱怨，其次是关于 Kotlin 如何使它更简洁的讨论。

我们让人们把他们自己和其他人放在前面，为未来的会谈提出建议，增加成员的方法，其中之一是创建一个 [Meetup 页面](https://www.meetup.com/Edinburgh-Kotlin-User-Group/events/263449151/)。

我们现在正在计划我们的第二次聚会，已经有三位演讲者准备好了更多。目前每两个月一次，直到会员和发言人名单有所增加，但进展顺利。

附言:如果有人感兴趣，我们正在寻找与科特林相关的演讲者。
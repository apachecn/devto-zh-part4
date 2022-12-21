# 每周笔记- 03 制造商

> 原文：<https://dev.to/ruthmoog/weeknotes-03-makers-2l4l>

# 第 3 周的 WebApps

这一周的每一天都有一个提醒，通过问这些问题来充分利用在 Makers 的时间:

1.  你玩得开心吗？
2.  你是一个比昨天更好的程序员吗？

对于任何学习努力来说，这些都是很好的问题。

## 在浏览器中调试

本周，我已经把学到的所有东西都投入到了 web 应用程序的创建中，并且不得不提升我的调试技能，因为我使用了更多的“ [stack](https://hackernoon.com/what-is-a-full-stack-developer-7b7750aa4c98) ”。通过将可能的错误分为两大类，选择采取何种措施来解决它们变得更加简单:

1.  **代码中的错误**(例如，语法或名称错误，这些错误带有有用的错误消息)以及，
2.  **意外行为**(预期与实际行为不同)

现在我正在使用 [MVC 模型](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)，在我的程序中有更多的地方会出现错误，也有更多的地方可以检测到错误:功能测试(我们一直在使用[水豚](https://teamcapybara.github.io/capybara/)和 RSpec)、单元测试、林挺，以及在浏览器中运行代码。

> 💡坚持调查，直到你对自己的改变有信心。

## 如何不惹怒你的团队

Makers 的校友 Dani Booysen & John Forster 在午餐时间发表了题为 ***如何不惹怒你的团队:一个初级开发者的非官方幸福指南*** 的演讲。我喜欢听他们讲述创客之后的生活经历，以及他们作为专业开发人员的一些畏缩时刻。他们的一些建议:

*   不要迟到(要尊重，要有动力)
*   寻求帮助(找出在升级前你应该被困多久)
*   不要把恶心的东西放在别人的桌子上(要有礼貌)
*   记笔记(你不会记得所有的事情)
*   参加社交活动(建立融洽关系)
*   阅读风格指南(如果你不明白，问*为什么*某事是为什么——决策通常是有原因的)

## 流程建模

想要抽象一个过程，使其易于理解，并且在没有什么先验知识的情况下也能理解？进入:*流程建模*。

[![Bumblebee Lifecycle from BBCT](img/a35875dbb15aff63315feaa242079b91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F5Fkv0CV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wcyizgnezba5148j3uby.jpg) 
<sub>*大黄蜂生命周期，图片来源[大黄蜂保护信托](https://www.bumblebeeconservation.org/)*</sub>

对于这一点，图表可能是首选，但过程可以在任何媒体中建模(制造商鼓励解释性舞蹈，但没有人会咬)。本周我们专门研究了服务器客户机关系的建模，使用一个示例用户旅程和浏览器开发工具来研究客户机和服务器之间的操作。

[![HTTP requests client/server model sketch](img/d157fd90b54b08ad3030955b94c98b3f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LkjdRe8l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/97e5s5gmk4e3ye87x1e3.png) 
<sub>我组在创客上画的一个客户端/服务器流程模型草图。</sub>

## 杂项

💪我度过了一个艰苦的周末，但我一直在很好地照顾自己🌱我种了一些辣椒，它们看起来很开心！
🔦同理心的开发者建立盟友，建设更好的 UX。
🚋早班火车提供了更安静的旅程
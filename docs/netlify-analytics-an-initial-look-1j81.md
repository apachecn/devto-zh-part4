# 网络分析——初步观察

> 原文：<https://dev.to/raymondcamden/netlify-analytics-an-initial-look-1j81>

前几天(哇，就两个，真的？)Netlify 宣布了他们平台的一个全新功能，[分析](https://www.netlify.com/products/analytics/)。正如你所想象的，这是一个专注于为你提供网站分析的功能，主要好处是可以跳过使用像谷歌分析这样的客户端库。

我使用 Google Analytics 已经十多年了，虽然我喜欢这个产品，但它并不总是最容易使用的。我把这归咎于我没有花时间去学习更多的产品，但我总是希望它有一个更友好或简单的界面。事实上，我已经在这里写了*多篇*关于从 GA 中提取数据和呈现更简单视图的博文。

所以当我听到这个消息时，我非常激动。到目前为止，我已经非常喜欢我用过的每一个 Netlify 特性，并且认为它也会很棒。不幸的是，这不是一项免费服务。现在，我说“很不幸”,但公平地说，Netlify 给了你一大堆真正好的免费功能。上周我在一个测试站点部署了一个无服务器功能。免费的。我会随时选择免费层无服务器分析。话虽如此，我仍然希望他们有一个免费层。我很乐意使用这个功能来跟踪较小的“玩具”网站，但我不认为我会为那些页面浏览量少于几百的网站这样做。

价格绝对合理——九美元。这个价格是针对每月页面浏览量少于 25 万的网站的，所以它应该涵盖大多数人。对于超过这个数字的网站，我不知道。[定价页面](https://www.netlify.com/pricing/#analytics)显示“定制”,因此可能会根据具体情况而定。

这对我来说相当重要，因为谷歌分析报告说，我的平均每月页面浏览量不到 10 万次。我过去常常徘徊在 130K 左右，但我的流量在去年左右开始慢慢下降。

所以想象一下，当我启用分析(稍后会详细介绍)并看到数字*明显高于这个数字*时，我有多惊讶。事实上，现在我每个月的浏览量明显低于 30 万次！我期望我的流量比 GA 报告的要高一点，因为人们阻止了 GA 和其他原因，但是我的天啊！

也就是说，虽然我绝对喜欢分析功能，并计划禁用 GA，并乐意为它支付 9 美元，但我现在已经发出了一封电子邮件来支持，看看我的价格是否会保持不变，尽管有点超过第一级限制。

好吧，你得到了什么？

当你启用分析(对我来说需要输入我的信用卡)，你立即得到统计数据。每一个统计数据都标有一条信息，说明它是不完整的，当你重新加载时，你可以看到数据变得越来越完整。我估计完成这个过程大约需要 30 分钟。

目前的数据只有一个月。我不确定几个月后会发生什么。我希望能够指定一个自定义的日期范围，或看到“上个月”与“去年”，但现在是过去三十天，仅此而已。统计数据绝对接近实时。我在美国中部时间下午 2:28 写这篇博客，我有下午 1 点的数据。所以不是现在，但我没意见。“实时”GA 报告非常简洁，尤其是当你因为写了一些很酷的东西而受到抨击时，但在我的博客生命周期中，这种情况可能发生过三次。(例如，我在 2008 年更新的 iOS 上的[博客条目](https://www.raymondcamden.com/2008/07/11/So-far-iPhone-20-is-DOA)得到了 971 条评论。)

现在你有七份报告。第一个是顶级总结:

[![Top level stats for my site](img/483b745838d5e99d70da11005ee55ff9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KozHMUwd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2019/07/anal1.png)

接下来是浏览量的折线图:

[![Line graph showing pageviews per hour](img/2baf430657ce338f8080c63cdfc6f2c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lNY9ynZX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2019/07/anal2.png)

然后是独立访客的每小时图表:

[![Line graph of unique visitors](img/d05738a5ee16ba3247c701ba1bb7c07d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gNi9H2wf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2019/07/anal3.png)

接下来是你的首页列表。这对我来说非常有趣。我注意到的一件事是，我现在的内容没有我以前的内容受欢迎。我很高兴看到今年的前十名中有三个条目。我仍然希望我最近的内容更多，但它比我想象的要好。

[![Top ten requested pages.](img/744247bdab602b71aa208f5cc0df70c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J0EQcSAx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2019/07/anal4.png)

注意我的前四个词条都和 Vue.js 有关，(Woot woot！)接下来是 404 报告:

[![Missing pages report.](img/c3cd8399458e3994d982845311aaf9ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vrVrh9ES--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2019/07/anal5.png)

我已经纠正了一些错误。然后有一个来源报告。我真的很惊讶来自 dzone 的金额。

[![Source report](img/789763b91a24fa560dfe3936055878e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9r9bMTkm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2019/07/anal6.png)

最后是带宽报告:

[![](img/3ac74ac29a0b448808870f46b8a77a4a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CpvJW8wQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2019/07/anal7.png)

你会注意到那上面的线条似乎有点偏离。它从 750MB 到 1GB 再到 2GB，规模似乎不对。我想这是个小问题。

反正现在就这样了。正如我所说，我认为这是一个非常好的服务，使我的数据比谷歌分析更容易处理。价格是…公平的。绝对公平。但我不会在玩具/演示网站上使用它，除非他们有一个免费层。(我可能会考虑创建一个“演示”网站，下面有多个演示。)显然，这是全新的，而且才过了两天，但一旦我确认了我的账单，我就已经被说服去取消谷歌分析了。

*标题图片由[斯蒂芬·道森](https://unsplash.com/@srd844?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 Unsplash* 上拍摄
# 星期五前端:仲夏版

> 原文：<https://dev.to/kball/friday-frontend-middle-of-summer-edition-1ckh>

希望你有一个伟大的一周，并有一个惊人的夏天！这周我为你准备了一些超级有趣的文章。我最喜欢的是 CSS 中的内在响应性文章，JavaScript 中的高级 Vue 案例研究，以及其他精彩内容中的代数效果文章和 Slack rebuild 案例研究。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

附注:接下来的两周我会出差。别急，简讯还是会发出去的！但我可能会对任何邮件或询问反应迟钝。干杯！

#### CSS&SCS

##### [调试 CSS 网格第三部分:自动流程、顺序和项目放置](https://css-irl.info/debugging-css-grid-part-3-auto-flow-order-and-item-placement/)

这是一个伟大系列的第三部分，讲述 CSS 网格的不同部分。我发现这非常有帮助，尤其是讨论项目的显式放置和自动放置的交互的部分。

##### [内在响应 CSS 网格用 minmax()和 min()](http://evanminto.com/blog/intrinsically-responsive-css-grid-minmax-min/)

“内在响应”，有时也被称为流体设计，是我最喜欢的网页设计/开发的新领域之一。这是创建响应屏幕大小的站点和应用程序的能力，而不需要显式的媒体查询和断点。我不认为所有的东西都符合这种模式，但是越多的东西越好。

##### [CSS :not()带多个类](https://css-tricks.com/css-not-with-multiple-classes/)

一个简单方便的小技巧，展示了如何通过链接`:not`断言 CSS 中的多个“not”子句。不过要注意具体性——这可能会增加很快！

##### [异步加载 CSS 最简单的方法](https://www.filamentgroup.com/lab/load-css-simpler/)

好吧，这更多的是关于加载 CSS 而不是编写 CSS，但我认为这是一个很好的技巧，一个应用起来非常简单的技巧，假设你有想要异步加载的 CSS。

#### JavaScript

##### [JavaScript 最新数据类型的基本指南:BigInt](https://www.smashingmagazine.com/2019/07/essential-guide-javascript-newest-data-type-bigint/)

我不认为我曾经在前端项目中工作过，但我确实在后端项目中工作过，这可能是一个问题，我可以肯定地想象这是一些前端情况下的问题。幸运的是，我们有了一种新的类型:BigInt to the rescue！

##### [了解服务工作者生命周期](https://medium.com/@vipulnema2610/understanding-service-worker-life-cycle-b6580aa4eb50)

服务人员非常有用，但他们的生命周期也与我们在网络世界中习惯处理的其他事情非常不同。绝对值得一读&仔细考虑…特别重要的是要记住，如果你有一个错误的，你需要在回滚时明确地取消注册它！

##### [共享父数据时链接 JavaScript 承诺](https://www.thepolyglotdeveloper.com/2019/07/chaining-javascript-promises-while-sharing-parent-data/)

如果你对承诺非常满意，这可能感觉相对简单，但是如果承诺仍然让你困惑，这是一篇很好的文章。它遍历了将多个异步任务(在本例中是 API 调用)链接在一起的示例，其中后续任务依赖于第一个任务的结果。

##### [构建 React 应用时不要做的 10 件事](https://dev.to/jsmanifest/10-things-not-to-do-when-building-react-applications-58a7)

使用 React 构建时要避免的大量常见问题。其中大部分是 React 特有的，但有些也适用于其他框架。

##### [高级 Vue:控制父插槽(案例研究)](https://michaelnthiessen.com/advanced-vue-controlling-parent-slots)

我花了几遍才完全理解，但我很高兴我花了时间，因为这个模式非常有用。事实上，我想我以前也曾无意中发现过它，但没有完全理解它。

#### 提升链接

##### [宣布 TylerMcGinnis.com 的两个新课程——React 和 React Hooks](https://tylermcginnis.com/subscribe/?utm_source=Friday%20Frontend%20Newsletter&utm_medium=Email&utm_campaign=Jul_2019_launch)

TylerMcGinnis.com 最初的 React 课程于 2016 年推出，自那以来已有超过 8 万名学生参加，平均评分为 4.8/5。今天，他们很高兴推出它的继任者。他们全新打造的 React 和 React Hooks 课程将在 2019 年教你所有你需要知道的关于编写 React 的知识。[仅本周享受七五折优惠](https://tylermcginnis.com/launch-sale/?utm_source=Friday%20Frontend%20Newsletter&utm_medium=Email&utm_campaign=Jul_2019_launch)

#### 其他牛气

##### [对我们其余人的代数影响](https://overreacted.io/algebraic-effects-for-the-rest-of-us/)

在经典的丹·阿布拉莫夫风格中，这篇文章打破了一个非常复杂晦涩的话题，让它变得有意义，一路上让你大吃一惊。爱死了！

##### [选择工具](http://bradfrost.com/blog/post/choosing-tools/)

这是一个精彩的帖子。我 110%同意作者 Brad Frost 在这里所说的——在我们的世界中，我们有一种倾向，即沉迷于我们喜欢的工具，并试图将它们应用于每种情况，很少问什么工具适合任何特定的工作。这并不总是微不足道的，与不同工具相关的学习曲线会使这变得困难，但我们应该将我们的学习视为建立一个可以应用于多种情况的工具组合，并使我们的部分学习专注于哪些工具适合于哪种情况。

##### [当重写不成:重建桌面上的懈怠](https://slack.engineering/rebuilding-slack-on-the-desktop-308d6fe94ae4)

迷人的幕后景象，既有重写大量使用的应用程序的过程，也有在此过程中做出的特定架构选择。所使用的方法是增量式的，无所不包的，一次重写一部分到应用程序中逐渐增长的“现代”部分，使用 React 和 Redux。结果呢？内存使用减少 50%,加载时间提高 33%……不算太差。

##### [一个人领导者的主动倾听指南](https://open.buffer.com/active-listening/)

“我有一个很想提拔的高级工程师，但他不够善于倾听。”这是一位经理的直接引语，我在研究技术线索的材料时与他交谈过。对于开发人员来说，倾听是一项非常有价值的技能，尤其是当你往上爬的时候，或者如果你想成为独立/自由职业者的时候。虽然这篇文章声称是针对经理的，但我认为无论你处于职业生涯的哪个阶段，善于倾听都会对你有所帮助。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
# 4 个重要的软件设计原则和快速解释

> 原文：<https://dev.to/skill_pathway/4-important-software-design-principles-with-quickfire-explanations-552o>

理解编程中的设计模式给了我们在许多不同的环境中工作的工具，以提高我们代码的整体质量。

我们认为你在网上找到的解释太复杂了，所以我们把简化这些解释作为我们的(副业)任务，这样每个人都可以理解。今天我们来看看 5:

## YAGNI(你不会需要它)

**现实世界的例子**
想象一对夫妇——杰克和吉尔。

杰克一直生活在对世界末日和黑死病死灰复燃的恐惧中。

他建了一个末日避难所，去哪都穿着生化服。他有一种“你永远不会太安全”的态度。

吉尔对杰克和他对那些*可能永远不会发生的事情*的过度补偿感到沮丧。吉尔明白为未来做准备的必要性，但还没到你投入大量精力去做一件极不可能提供任何有价值回报的事情的地步。

**用编程术语**
好吧，那是一个极端的例子。在商业和个人项目的世界里，情况稍微微妙一些，所以你需要在你工作的商业或项目的背景下考虑 YAGNI。

也就是说，如果你正在为一家资金充足的初创公司工作，该公司有一个 3 年计划，并且对未来的发展有明确的规划，那么提前几个月做好预防措施可能是有好处的。

如果这是一个个人项目，你可能不需要那些花里胡哨的东西——最好专注于核心功能。

这里的推理可以颠倒过来——这通常是优秀的 CTO 和高级开发人员能够发现的。这实际上取决于业务/项目的需求。

以模块化的方式编写代码也是很好的，在这种方式下，您不必被迫按照预先要求来编写功能。如果您必须为未来几个月的事情编写预防代码，这可能是一种“代码味道”。

# 依存倒置

**真实世界示例**
假设有两家银行——“A 银行”和“B 银行”

为了从 A 银行取钱，你把你的卡放进 ATM，输入你的个人识别码，然后你会看到一个登录到 MySQL 服务器的终端/命令行。从这里开始，您必须编写一个更新查询来从您的银行余额中扣款，然后机器会将您的钱交给您。

为了从 B 银行取钱，你把你的卡放进自动取款机，输入你的密码，在密码键盘上输入你想要的钱，机器就会给你钱。

第一个违反了这个原则，第二个没有(你可能已经猜到了...)

**用编程术语来说**
要理解的关键是**你的类和函数不应该知道它们的依赖关系**。

这导致了所谓的“紧耦合”,如果你想为不同的依赖项切换出你的依赖项，这就成了一件痛苦的事情，你必须有效地重写所有使用它的东西。

就像现实世界中的例子一样，如果银行 A 决定从 MySQL 迁移到 MongoDB，它的所有客户现在都必须学习如何编写 Mongo 查询。

在编程世界中，如果您的“商店模型”只能与“MySQL 连接”一起工作，如果您突然切换到基于 Mongo 的架构，那么使用该连接的其他 40 个模型现在需要更改为“Mongo 连接”

相反，它们应该使用“DBConnection”接口(定义所有数据库连接的东西),某种容器应该给出当前的实例。这使得改变事情变得更加容易

# 干(不重复自己)

**现实世界示例**
假设有两个社交媒体代理机构——代理机构 A 和代理机构 B

当机构 A 在团队中雇用了一名新的社交媒体经理时，该经理通过观察团队中的其他经理并复制他们的流程来学习社交媒体策略。

如果 A 公司的老板想改变流程，他会和每个人坐下来，告诉他们这些变化。有时他忘记告诉一两个经理，这意味着他们正在执行多余的流程。

当机构 B 在团队中雇佣了一名新的社交媒体经理时，该经理通过阅读每个人都可以访问的单个 google 文档来学习。

如果代理商 B 的老板想要对流程进行更改，他将在 google 文档中进行更改，并在 Slack 频道中标记每个人以阅读这些更改。这意味着他一次性更新了所有人。

代理甲违规干，代理乙坚持干。

**用编程术语来说**
如果你发现自己编写的代码一遍又一遍地做着同样的事情，你可能会想在你的应用程序中把这些功能放入一个可重用的类、方法或函数中。

情况并不总是这样，对于何时应该和不应该这样做，实际上有不同的意见，但是作为一个新的开发人员，开始考虑在哪里可以使代码可重用是非常好的。你会发现其中的细微差别，尤其是当你和其他有经验的开发人员一起工作的时候。

# 单一责任原则

真实世界的例子
菲尔是一名平面设计师。他的老板希望他还能管理社交媒体账户，在他打高尔夫球时照看他的狗，帮助进货和挤奶。

菲尔的责任太大了；你可能会说他违反了单一责任原则。

尽管这是他老板的错，他应该雇佣专家来承担这些责任。那就是坚持单一责任原则

用编程术语来说
无论你是函数式程序员还是 OOP 程序员，你都将在你的应用程序中构建“功能块”——无论是函数、类还是任何其他执行符号。

如果你试图描述你的一小段代码的职责，并且你提出了超过 1-2 点，你可能违反了 SRP。

参考消息:这是一条随着经验而变得更加清晰的线，但是你学习这条线的捷径是通过查看主要的开源库，并试图解释你认为每个文件/类等的责任是什么，你将开始发现人们开始把事情分开的模式。

# 给我们一些你想简化的其他编程原则，或者告诉我们在这四个方面做得更好，如果你仍然不能得到它们

我们真的想尝试并帮助每个人获得这些“中级开发人员原则”——因为如果有人能提供书面解释，他们不一定是中级的。

*   还有哪些你觉得难以理解的原则？
*   哪些是我们在这篇文章中解释得不够好的？

感谢您的反馈，并帮助我们整理对您有用的内容！

# 还有其他你觉得难以理解的编程设计模式吗？

让我们在下面的讨论中知道，这是我们的“傻瓜编程原则”系列的第一篇文章，我们想让尽可能多的初学者能够接触到它们。因为它能让我们写出更好的代码！

# 好奇前端开发？我们刚刚发布了一个免费速成课程(不，它真的是免费的——没有升级，没有隐藏成本)

我们花了几个月的时间制作了一个*专业设计*和 *4 小时 20 分钟*的视频内容，为您带来我们的免费速成课程，向您展示如何编写这个投资组合网站(我们不断鼓励您根据自己的风格进行定制)

如果你对此感兴趣，并且想了解更多关于 HTML、CSS、SCSS、Bootstrap 4、Git、GitHub 页面以及一些 JavaScript 和 jQuery 的知识，你可以今天就通过这里的链接免费注册。
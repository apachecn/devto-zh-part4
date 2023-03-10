# 千年网络应用编程接口架构

> 原文：<https://dev.to/vitcord/web-api-architecture-for-millenials-2n9>

在 Vitcord，我们始终努力为用户提供最佳体验。最近，我们发现推送通知管理开始在我们的 monolith 中造成瓶颈,所以我们决定在问题变得更糟之前面对它。

当我们开始研究这一挑战的解决方案时，推送通知并不是我们 monolith 的一大部分，所以我们意识到将其提取到一个独立的微服务可能是一个好主意。此外，一旦你有了一个有趣的用户群，推送通知就成了成功留住用户的关键。从我们的主服务器中提取这一职责意味着我们可以单独扩展这一部分，并给予它更好的维护。

一旦我们决定要建造什么，我们就开始考虑如何去做。从一开始，我们就知道我们的数据库应该是 Cassandra 或 DynamoDB，因为通知和消息传递是这种数据库的特性。选择语言和框架确实花了我们一点时间，但最终我们选择了科特林和 Spring Boot。这两个是完美的一对，我们的一些技术人员有一些使用 Kotlin 的经验，此外，Android 团队的应用程序完全使用这种语言。

## 选择合适的架构

当我们开始设计系统时，我们知道它不会有太多的动作(基本上每种类型的推送通知都有一个动作)，但将来会更多。我们知道面向垂直的方法非常适合，因为它允许轻松地分配工作，插入或拔出动作，并单独维护每个垂直，而不需要接触更多的代码。

经过一些研究，我们发现了一个非常适合我们设计的架构模式:Action Domain Responder。这种模式是作为经典模型视图控制器的进化而诞生的。

MVC 是 90 年代诞生的用户界面应用程序的架构模式。控制器维护程序的状态，告诉视图它应该如何显示给用户。随着状态的每次改变，UI 应该自动呈现。由于像 Ruby on Rails 这样的框架，它在后端变得非常流行，web 应用程序也遵循这种模式。

[![](img/94ab6f7b48e2ce1851f6b93949e7d2d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uWx-LUZo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/700/0%2AP9aPAwygaZVfEq5x.png)

## 更好的 Web MVC

[Action Domain Responder](http://pmjones.io/adr/?source=post_page---------------------------) 创建于 2014 年，是 MVC 在 web REST APIs 环境下的一个改进。REST 的一个关键原则是它是无状态的，每个 HTTP 请求都被独立处理，所以不需要保存状态。ADR 为后端应用程序提出了一个更加自然的流程:

1.  该操作接收一个 HTTP 请求。它收集任何需要的输入，并用它们调用域。

2.  域操纵信息并应用任何业务逻辑。它向操作返回一个结果。

3.  该操作使用域的结果调用响应者。

4.  响应者构建一个 HTTP 响应并发送它。

[![](img/dd6471eb738e2114df75672b017d715f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dSm1YNzW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/528/0%2Aw0L6jYVaNl2bZPXT.png)

ADR 适合我们的垂直导向方法，因为我们可以最大限度地履行 SOLID 的单一责任原则:

*   在 ADR 中，每个动作是一个单独的类，只有一个方法，而不是一个控制器，每个端点有一个方法。操作仅将域与响应者连接。

*   我们可以按照命令模式对领域建模，这有助于我们隔离业务逻辑并重用它。

*   响应者唯一的工作是基于输入构建 HTTP 响应。它们可以是纯函数。

每一个垂直都是一个动作、一个响应者和一个命令的集合。这允许我们添加或删除它们，而不会影响更多的组件。测试也变得很容易，因为每个部分都有一个有限的职责，他们通常有一两个方法。结构也是另一个优点，因为通过相应的动作很容易找到每个文件，你可以从动作的顶层外观看出应用程序的每个功能。

## 理论虽好但...给我看看代码！

[![](img/625d73a0de2bc6ac4094e6bc832ef2a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SKXa2BUP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/700/0%2AshKoi9dvAF1SWwOR)

**Actions** 定义了它们正在监听的端点，并且只有一个 *execute()* 方法将被每个请求调用。它们用请求输入调用域，然后用结果调用响应器，最后返回响应器构建的响应。

[![](img/7e0136fc80f081dfc0f6c1cba3d2672e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6Q9Tv7xe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/700/0%2AugAtM6txZN8zCJPx)

**域**按照命令或用例模式划分成类。他们负责操作数据，将数据存储在数据库中，发布领域事件等。作为动作，它们总是有一个 *invoke()* 方法，该方法从 [Arrow](https://arrow-kt.io/?source=post_page---------------------------) 返回一个数据类型，这是 Kotlin 中的函数式编程库。

**或者**代表某个操作可能失败或者成功。它就像一个黑匣子，我们在里面操作，但我们不知道是否一切顺利，直到我们打开它。这比抛出异常要好得多，因为我们明确地告诉我们的代码可能会失败，我们强迫自己处理每一条路径。

在这个具体的例子中，我们告诉这个命令，如果一切正常，它可以返回一个通知，或者它可以从我们创建的**代数数据类型(ADT)**(*错误*)返回一个错误，这意味着错误必须是父类的密封层次结构下的一个对象。

[![](img/930a67b3d84abc4c864c7126f209e0e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sSkPhfmS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/700/0%2An2yALTMJFf1svd2c)

**响应者**只需要打开任一个盒子，并根据其内容创建一个 HTTP 响应。为此，我们使用了 *fold()* 方法，该方法接受两个 lambdas 并执行其中一个，这取决于内容是错误还是成功。

由于 Kotlin 的 *when* ，以 ADT 风格对错误建模迫使我们处理每一种情况。如果我在层次结构中添加了另一个错误，响应器不会编译，直到我在 *when* 块中处理它。

## 总之

我们认为 ADR 是比 MVC 更好的 REST APIs 架构模式，它非常适合我们的开发工作流程。它允许我们在很短的时间内部署这个项目，并且由于责任分离、可测试性和架构给我们的良好结构，它将很容易维护。

感谢你阅读这篇文章。如果您喜欢 ADR 模式，请通过评论或 [tweet us](https://twitter.com/vitcord_tech?source=post_page---------------------------) 给我们反馈。请继续关注并关注 [Vitcord Tech](https://dev.to/vitcord_tech) ，获取更多关于我们的经历和学习的帖子🙌
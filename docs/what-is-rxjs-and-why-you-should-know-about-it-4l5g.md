# RxJS 是什么？以及为什么你应该知道它

> 原文：<https://dev.to/thisdotmedia/what-is-rxjs-and-why-you-should-know-about-it-4l5g>

在前端开发的广阔世界中，有许多有趣的工具和资源可供选择。为了用我的学习策划一个强大而实用的方向，我与 RxJS 社区中最杰出的人物之一 Ben Lesh 坐在一起，以了解更多关于 RxJS 的信息，以及作为一名初级开发人员，学习并将其融入我的代码中是一个很好的时间投资的原因。

他指导我如何应用 RxJS，并告诉我如何学习。

**问:RxJS 解决的是什么问题？**

答:编码就是解决问题和找到正确的工具来解决正确的问题。在 RxJS 的情况下，正在解决的问题是处理具有多个事件的异步调用的能力。但是这到底是什么意思呢？

假设您正在编写一个函数，该函数携带一系列动作的数据，但是出现了一个错误。如果您只是使用函数来处理一系列请求，可能会有一些不必要的步骤返回错误。它应该能够接受错误并更新视图，而不是通过所有函数传递错误，而不需要运行现在不必要的 Ajax 请求。

您可能会注意到，承诺解决这种类型的错误处理，但 RxJS 将连续动作的概念提升到了一个新的层次。一个承诺只能处理一个值，这限制了它的用例。此外，承诺是不可取消的，这意味着它有可能阻塞线程并耗尽不必要的资源(对于资源受限的设备，这是重要的考虑因素)。

相反，RxJS 提供了一个解决这些限制的方案，它提供了一个以上的通信通道，以一种有效的方式简化了多步骤事件的处理。RxJS 还为开发人员提供了将任何需要触发事件的东西视为单一形状的东西的能力。当一切都是相同的形状时，组合、合并和查询这些东西就变得非常容易，从而产生一个非常强大的工具。

**问:为什么要学 RxJS？**

答:从好的方面来看，它是一个强大的工具，可以将复杂的一系列动作转化为易于操作的简洁代码。不利的一面是，有很多语言需要花时间去学习。然而，当你意识到用一行代码，你可以做一些事情，比如构建一个拖放，它接受三组事件并将它们协调在一起(鼠标按下，鼠标移动，鼠标抬起)以生成一行简洁的代码，这是值得的。否则就是几十行代码。

问:使用 RxJS 有什么好处？

答:将 RxJS 集成到您的代码库的最有吸引力的特性之一是，您使用它的次数越多，您可以用它做的事情就越多。RxJS 在某种意义上类似于使用乐高，因为所有的积木都是相同的形状，所以乐高非常适合创造性的建造。类似地，所有可观察的事物都有相同的形式，所以用可观察的事物来建造就成了一个诱人的命题，因为你可以考虑许多创造性的解决方案。在整个代码库中使用的 observables 越多，在现有结构上构建的选项就越多。

问:如果一个人想把可观察的东西集成到一个大的代码库中，会是什么样子？

答:你可以在几乎任何应用程序中使用 observables。作为一个团队来适应可能需要一点时间，但是如果你:

-从简单且适当的可观察应用开始
-对行动进行良好的评论
-愿意与您的团队合作，就您正在做的事情对每个人进行教育

对你的团队来说，这应该是一个平稳且有帮助的步骤。每个人可能需要一段时间来自己学习，但是如果你看到你的团队开始“接收一切”，不要惊讶。当动作使用可观测量时，它们都采用相同的形状，随着它在你的应用中变得无处不在，使用起来变得更加令人兴奋。

问:使用可观察的东西从来都不是一个好主意吗？

甲:当然可以！如果它是一个单独的事件，并且只做一件事情，就像很多 JavaScript 编程一样，那么 RxJS 就有点傻了——你可以使用它，但是它可能有点过头了。

拖放是使用它的一个很好的例子。这是一个具有多个动作的事件，要求尽可能降低复杂性。

问:你如何判断何时应该使用可观测量？

答:根据上下文来限定何时使用可观测量是有帮助的。例如，
-如果您的操作触发了多个事件，请使用 RxJS
-如果您有很多异步，并且您正在尝试将它们组合在一起，请使用 RxJS
-如果您遇到了需要被动更新的情况，请使用 RxJS
-如果您正在处理数组中的大量数据，并且需要分步处理这些数据，您可以使用 RxJS 操作符作为一种转换器，它可以处理这些数据集，而不会创建中间数组，这些数组需要在以后进行垃圾收集。

问:在进入 Rx 运营商的广阔世界时，应该从哪里开始？

答:您的操作列表应该包括映射、过滤和扫描。其他重要的操作符有:
-切换映射
-串联
-共享/共享重放

您可以将运算符的使用范围缩小到少于 10 个最常用的运算符。总有一些奇怪而深奥的运营商可供选择，比如 pairwise、bufferCount 和 groupBuy。它们的存在是有原因的，只是你不经常用。然而，在您确实需要执行这些功能的情况下，您不必亲自构建它。

问:在 React 这样的框架中开始编写 RxJS 有多容易？
答:在 React 中实现 RxJS 与在 Angular 中实现非常相似，后者已经被完全采用。在 Angular 中，可观测者是一等公民，所以在 Angular 中使用它会特别容易。但是，这就像在 ComponentDidMount 上订阅 RxJS，在 ComponentWillUnmount 上取消订阅一样简单。这与在 Angular 中手动实现的想法是一样的。

**问:有什么调试 Rx 的小技巧吗？**

答:像任何事情一样，随着对它了解得越来越多，使用起来越来越得心应手，调试也变得越来越容易。有些情况很难调试。我目前正与 Chrome 开发团队合作，为这些特殊情况制定解决方案。

人们遇到的一个常见障碍是，当你从一个合并映射返回一些东西，它期望一个可观察的，但它返回一些不是可观察的，人们希望能够看到函数返回他们认为是可观察的，但不是。目前，没有办法显示这一点，因为在它返回之前，您无法知道被返回的内容的形式。对于这种情况的建议是:
-使用大量控制台日志
-将 do 操作符组合到可观察链中，以便您可以看到每个步骤中可观察对象的位置以及返回的内容。

**问:Rx 的未来会是怎样的？**

答:RxJS 的未来有几个变化值得期待。出租运营商将很快推出供公众使用。不是有操作符，而是有一个映射操作符在一个可观察对象上，你会得到一个映射函数，当你调用它时，它会返回另一个函数，一个可观察对象会用它来做完全相同的工作。例如，您不用编写 observable.map.filter.scan，而是编写 observable.compose(map，filter，scan)。这是一个非常强大的变化，因为当你得到函数构建函数时，许多函数编程的机会就出现了。这个改变的另一个好处是更好的树抖动。现在 RxJS 还没有出现这种情况，因为一切都停留在原型上，所以你可以做点链接。所以即使你没有使用它，它也会假设你在使用它。

**问:RxJS 的日益流行如何改变 React 和 Angular 的格局？**

答:随着 RxJS 变得越来越容易访问和集成，我希望它会在整个前端社区变得更加普遍。目前使用 RxJS 的两个最大障碍是学习曲线和它占用的空间。RxJS 已经存在了很长一段时间，但是之前核心团队发现很难弄清楚如何以一种可理解的方式将它送到程序员手中。Rx 的创造者很聪明，但是他们使用了复杂的术语，这导致了普通开发者缺乏参与。由于像 Dot 这样的学习资源越来越受欢迎，开发人员采用这一非凡工具的人数大幅上升。

为了进一步简化这种采用，我目前正在努力减小 RxJS 的尺寸。你可以期待 Tiny Rx——或 T-Rx 的消息，它将 24k (g-zipped)库压缩到只有 3k！

看到 Rx 在整个社区越来越受欢迎并得到实施，这是非常令人兴奋的。虽然人们对它的兴趣越来越大，但要在硅谷以外的社区广泛使用它还有一段路要走。成为这种强大工具的成长和发展的一部分将会很有趣。

现在你已经尝试了 RxJS 提供的可能性，如果你有兴趣了解更多，请查看更多资源，如 [Rx 工作坊](https://www.rxworkshop.com/)、[Rx 简介](https://www.youtube.com/watch?v=K7AvXUNB2X8)、[反应式思考](https://www.youtube.com/watch?v=3LKMwkuK0ZE)、 [RxJs 深度](https://www.youtube.com/watch?v=KOOT7BArVHQ)。

受访者
[本·莱什](https://news.thisdot.co/u/da6839d28258)
谷歌
RxJS 5+项目负责人软件工程师

作者
Necoline Hubner
反应学徒在这个点
@necolinesan

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](//thisdot.co) 查看我们的服务列表。
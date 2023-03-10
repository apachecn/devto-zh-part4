# 为什么你现在应该使用 Node-RED！

> 原文：<https://dev.to/poojamakes/why-you-should-be-using-node-red-right-now-1n1l>

这个月，我花了大部分时间探索、学习和教授 Node- RED，我得出的结论是，这种令人敬畏的技术是你现在应该使用的！

[![](img/c9d1e90c53de51f15285a7d64e92eb46.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YoVMpGwV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kb2qi0ge5lhcgup1ykgv.png)

让我解释一下。您是否曾经想要快速原型化某个东西，无论是构建物联网设备、web 服务，还是为您的企业客户提供更大的概念验证？你有没有发现自己在谷歌上没完没了地搜索如何开始或试图找出你的项目将在哪里运行？有了 Node-RED，您可以在几分钟内开始构建、原型制作和共享。

由 IBM 设计和制造的 [Node-RED](https://nodered.org/about/) 是一个免费的开源逻辑引擎，允许任何级别的程序员互连物联网、基于云的系统、web 服务、数据库、API 等！这里有 3 个使用起来如此棒的原因！

**1。Node-RED 的强大之处在于它的易用性。**

使用 Node-RED 非常简单，只需将图形化的*节点*连接在一起，然后部署即可看到想要的结果。它使用了一个可视化的网络界面，在这个界面中，被称为*节点*的块可以接收和发送消息给其他节点。这使得整个集成过程变得更加容易，因为它抽象出了大量重复的样板代码来完成这些简单的事情。

任何有逻辑或编程背景的人都可以很容易地开始使用 Node-RED。节点托盘中有无数的节点可以帮助您构建有价值的东西。这些节点允许您注入自己的数据或来自其他来源的数据(即社交媒体、物联网设备、API 和数据库)。您还可以使用功能节点来编写自己的逻辑，以及构建自己的 web 服务用户界面。

下面用一个简单的 Hello World 举例:
[![](img/3021abb9f44e75a041b9b16b48a9a39f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NOedsWJF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/az3j9kvspdo2klq63zov.png)

在本例中，使用一个注入节点向一个功能节点手动发送一个消息对象，在本例中为“Hello World”，功能节点将 msg . payload“World”替换为“Everyone，I hope you enjoy Node Red”。然后，它将消息转发给一个调试节点，该节点将消息打印在 debug 选项卡上。

这个简单的流程可能非常强大，因为当您能够构建 hello world 应用程序时，您可以轻松地构建更复杂的东西，而不必学习如何使用几个 API 和库。只需能够拖放、连线和部署，您就可以开始了！

**2。Node-RED 允许开发人员在几分钟内完成原型、测试、构建和重建**
Node-RED 上提供的无数示例和节点使开发人员可以在几分钟内轻松测试他们的想法。

假设您想要构建一个应用程序来告诉您一条推文是正面的还是负面的。

您可以轻松地连接一个 twitter 输入节点，并将 tweets 输出到您的调试面板，如下所示:

[![](img/190d48895e8e483a275ca882fe9f653d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M1uNou-y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e2v8j3ssb1t8pjlw4gcy.png)

现在，一旦你有了推文的输入，你就要进行情感分析，因为这将会给你推文的情感，以及它是积极的还是消极的。

[![](img/bda987e7f2e9cbe0860c9562283af788.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QWCGYYTG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4p7gyqr9dmajvl9t96mf.png)

在这个例子中，我们连接了 inject 节点，用静态消息来测试情绪分析服务，以表示 tweets 的流入。我们可以很容易地向我们的流程添加测试，以确保我们的服务按照我们希望的方式工作。

现在让我们说，我们对这个流程的工作方式很满意，但是在一个仪表板/用户界面中表示数据会很棒。这个有节点！

[![](img/5b31b779d9b3cbe4635f3c7a5ad2a96e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6SNH74kA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/px16u1kwk9a0mk5a5krt.png)
[![](img/b8ae90a1380435ddffbc3c6141a2e6fb.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--y4AF9_g8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6vtyp6s6inot0h2zh8ml.png)

在这个例子中，我们从获取推文作为输入的想法发展到构建一个 UI 仪表板，显示情绪得分或正面或负面推文。与编写自己的 tweet 输入、情感算法和健壮的用户界面所需的时间和精力相比，我们可以轻松地连接必要的节点，并在明显更短的时间内获得工作原型。

当然，这个流程不是全部，但是它将向您展示这些服务是如何协同工作的。如果您决定使用原型进行更多的生产级工作，Node-RED 中的流程将为您提供一个可靠的路线图。在许多情况下，当构建一个新功能、一组功能或从浏览器到数据库的整个应用程序时，Node-RED 还会涵盖从原型到产品的整个过程。

Node-RED 最大的特点是能够随时调整、调整、构建和重建。准备好开始用健壮的原型给你周围的人留下深刻印象，这些原型可以在比你想象的更短的时间内变成生产质量的应用程序！

**3。Node-RED 非常灵活，基于可靠的技术**

Node-RED 是建立在最可靠和最普遍的技术栈之上的——JavaScript。这使得它非常灵活，易于在网络浏览器、服务器端、物联网项目等方面工作。

因为你将在这个领域工作，有来自 [npm](https://www.npmjs.com/package/node-red) 的对 Node-RED 的难以置信的支持，并且有超过 [3000 个现成的 Node](https://flows.nodered.org/?num_pages=1)让你开始建造你想要的任何东西。

如果你没有从成千上万免费提供的组件中找到符合你需求的组件，[你可以自己编写](https://nodered.org/docs/creating-nodes/)。

您可以将 Node-RED 作为 IBM 云服务运行，并轻松连接到 cloudant 数据库和 IBM Watson 服务，或者您可以在本地安装 T2 Node-RED。你也可以在 docker 中安装 [Node-RED。它高度灵活，易于运行和上手，因此没有限制。](https://nodered.org/docs/getting-started/local)

你有它！Node-RED 对任何想把他们的想法从概念变成创作的人来说都是非常强大和有用的！它易于使用，是原型开发的理想选择，而且非常可靠。所以，你还在等什么，现在就用 Node-Red 开始构建吧！

我希望这个博客能帮助你开始下一个原型之旅！查看我的 [Twitter 分析研讨会](https://node-red.gitbook.io/workshop/)来获得如何使用 Node-RED 构建 Twitter 分析器的信息。

如果你喜欢这篇文章，请关注我，或者在 Twitter 上关注我
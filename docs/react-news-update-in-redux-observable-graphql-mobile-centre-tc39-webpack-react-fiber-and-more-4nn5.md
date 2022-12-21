# REACT 新闻:Redux-Observable、GraphQL、Mobile Centre、TC39、Webpack、React Fiber 等的更新

> 原文：<https://dev.to/thisdotmedia/react-news-update-in-redux-observable-graphql-mobile-centre-tc39-webpack-react-fiber-and-more-4nn5>

萨梅尔·布纳、帕拉舒拉姆·N、尼哈尔·韦努戈帕尔、卡梅隆·韦斯特兰和杰伊·菲尔普斯等开发人员最近与我们的团队讨论了 JavaScript 的新发展。

特色视频将让您了解 GraphQL、Redux-Observable、React Fiber、TC39 进口提案以及新术语“可扩展 React”的介绍。

**GraphQL 和 ReactJS**

*萨梅尔布纳对 GraphQL 的价值，GraphQL 订阅量，ReactJS*

萨梅尔·布纳谈到了 GraphQL，并向我们展示了使用查询语言有多棒，以及它与 React 有多完美。该语言非常清晰地描述了确切的数据需求，并优化了前端应用程序和服务器之间的数据通信过程。总的来说，它是比 REST API 更好的语言和替代品。

萨梅尔还介绍了提供实时通信的 GraphQL 订阅，萨梅尔还谈到了允许从云中检索即时图形 API 的新兴应用程序。如果一个人正在寻找云托管他们的数据，现在有一些应用程序可以用来定义模型并获得所需的即时图形 API。这种开发消除了初学者的入门障碍，因为他们不再需要编写完整的服务器应用程序来使用 GraphQL 作为他们的前端应用程序，相反，他们可以简单地在云中生成一个 API。

React 证明自己是一个很好的工作框架，因为它既专业又灵活。与 Angular 或 Ember 不同，它没有现成的设计决策或提供完整的解决方案，这可能会限制开发人员的实验。

萨梅尔建议，一旦开发人员对 React 的来龙去脉有了信心，下一步应该是探索 Node。他认为，因为 React 的生态系统和许多新工具都是基于节点的，React 开发人员可以通过了解社区内的工具获得大量知识。

[https://www.youtube.com/embed/Oio0dfSvIYs](https://www.youtube.com/embed/Oio0dfSvIYs)

**微软的移动中心、浏览器性能和 React Fiber 工具**

*Parashuram N 描述了在微软从事移动中心、浏览器性能和 React Fiber 工具的工作*

在微软，Parashuram N 作为项目经理参与了许多项目。一个例子包括为 React 开发的 Visual Studio Code (VS Code)扩展，它允许作者直接从他们的 VS 代码中调试应用程序。正在进行的实验打开了在云上测试反应式应用的可能性。

移动中心是另一个来自微软的项目。这是一个与 VS 代码并行运行的系统，因此个人可以选择是使用其中一个还是将两者结合起来。Mobile Centre 允许开发人员选择他们想要使用的 Github 存储库，然后这些存储库被签名、构建、在云上测试，并分发给最终用户。

作为一名 web 开发人员有很多额外的好处，其中之一就是能够立即给用户带来变化。在呈现变更或新特性的过程中没有延迟，JavaScript 疲劳通常不是问题。部署代码的个人能够持续地这样做，而不会遇到挫折。开发实践和技术，如 LiveReload、热模块替换和浏览器同步，也使 web 创建更容易管理。所有这些好处都是 web 开发世界独有的，这也是 React 如此适合 web 的原因。

虽然应用程序确实是原生的和特定于 web 的，但是在移动开发领域中迁移到 react native 是相当容易的。原因是，可以使用代码推送之类的东西将想法立即部署给客户，并且可以使用许多工具(如 ChakraCore 中的时间旅行调试功能)来帮助这个过程。

也出现了许多与性能相关的项目，如 browser-perf，它使 web 性能或监控系统自动化；以及跟踪 React.js 库性能的 tv monster 应用程序。该应用程序自动收集所有框架和所有 react 版本的性能数据。它可以在 Chrome 和一些移动浏览器上运行。这些测试与 React 现有性能测试的不同之处在于，它们是从浏览器的角度进行的，而不是针对特定的 JavaScript。

Parashuram 还讨论了微软、RxJS 的理念，以及为 VS 代码上的 Redux Observable 构建开发工具。

[https://www.youtube.com/embed/pq38dSL7ZVk](https://www.youtube.com/embed/pq38dSL7ZVk)

**TC39 进口提案和 Webpack 2**

*TC39 进口提案、Webpack 2 和东部的 React 社区与 Neehar Venugopal*

目前，作为一名软件工程师，Neehar 的主要工作是为开发人员提供一个解决方案，帮助他们更高效地构建应用程序。他是 import-proposal 背后的作者之一，import-proposal 帮助作者发布更少的代码，并强调重要的代码以优化性能(特别是在移动领域)。

导入提议在 Webpack 2 中可用，并在 TC39 的第 3 阶段提议中，这意味着它还没有在浏览器中出现。然而，个人确实同意它的工作，所以它将很快实施。

进口提案的启动受到了关于 CSS 移动和 UI/UX 移动的讨论的启发。鉴于 javascript 的移动性尚待讨论，但移动应用程序的速度和性能需要提高，这个话题终于开始浮出水面。出现了一个问题:“我怎样才能只发送向用户显示屏幕上的内容所需的最少量的 javascript 代码”。还引入了动态导入，因此 webpack 中可能会发生延迟加载和代码拆分。

尼哈尔还谈到了东部的 React 社区。虽然离得很远，但是由于所有开发人员的参与、多样性以及团队成员之间的交流，该领域的开发人员仍然感觉自己非常融入社区。然而，在西方，可以改进的是更加注重表现。与 VueJS 不同，React 由于性能问题没有被广泛采用。

[https://www.youtube.com/embed/wEnIKjgmP2Y](https://www.youtube.com/embed/wEnIKjgmP2Y)

**可扩展反应**

*卡梅隆·韦斯特兰展示“可扩展反应”*

Cameron Westland 是 Autodesk 的一名软件架构师，目前正在 React 上开发一个新的 web 版本。引入了术语“可扩展的反应”，它可以被定义为“创建可插入的 web 应用的声明性方法”。

扩展性经常在应用程序中使用。然而，当涉及到构建可扩展的应用程序时，就没有太多的讨论了。Cameron 和他在 Autodesk 的团队努力引发关于这一主题的更大讨论，回答了一些问题，如“如果一个人有一个工具栏，并希望使用扩展将图标添加到工具栏，如果应用程序内置在 React 中，他/她将如何做？”。解决方案的一个例子包括被称为 Annihilus 的电子邮件客户端。它内置在 React 中，提供扩展，并允许个人将自定义部分添加到他们的无眠电子邮件客户端。

React 最大的优点之一是它对社区之外的生态系统的影响。例如，在 React 之前，许多 UI 框架都是整体的。它们遵循惯例，并且不是面向组件的。React 之后，许多框架开始改编和重写它们的应用程序，使之更类似 React。随着 React Fiber 的新变化，这种连锁反应肯定会出现。除此之外，React 社区也是一个思想开放、令人鼓舞的社区。没有一个人能决定反应会是什么或应该是什么。它有很强的团队活力，框架作者开发的很多想法都是由社区推动的。

[https://www.youtube.com/embed/UdPx-OVEn28](https://www.youtube.com/embed/UdPx-OVEn28)

**还原-可观察和反应纤维**

*杰伊·菲尔普斯论 Redux-Observable、React Fiber 和 ReactJS*

React 社区被描述为在接受新思想方面最开放的社区之一。它带来了彻底的反思，甚至在当时被认为是反模式。React 能够重新思考事情是如何完成的，并创造新的最佳实践。这些新规范已经被 Angular 和 Ember 等其他框架共享和采用。

Jay 讨论了一些在 React 生态系统中处于领先地位的开源项目。其中包括 redux-observable、React Fiber 和 Jest。

Jay 分享了 redux-observable(或 RxJs)的愿景，它是一个中间件，使用 Epic 作为本库的合著者来编写或取消异步副作用。Jay 和 Ben Lesh 受到了社区中其他想法的启发，例如 redux-thunk 和 redux-saga。

Jay 分享了他对 React Fiber 的看法，以及开发人员如何能够在渲染中优先考虑某些元素，如输入或动画。

Jest 是一个单元测试框架，为一个简单的测试系统创建快照或代码。这是一个项目的例子，像 React，最初没有被接受，但经过科学改造，现在成功了。今天，Jest 在其他测试框架中被模仿，因为它非常有用。

[https://www.youtube.com/embed/_BQL_TGvhqg](https://www.youtube.com/embed/_BQL_TGvhqg)

不要错过通过贡献更多地参与社区的机会！你可以在这里找到 React 库[。](https://facebook.github.io/react/)

*本文由[崔健](http://twitter.com/tkien001)和[李美玲](http://twitter.com/ladyleet)T5】共同撰写*

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](//thisdot.co) 查看我们的服务列表。
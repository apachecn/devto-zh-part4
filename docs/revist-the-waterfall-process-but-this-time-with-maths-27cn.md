# 修改瀑布过程，但这次用数学

> 原文：<https://dev.to/johnkazer/revist-the-waterfall-process-but-this-time-with-maths-27cn>

(封面图片由[约翰·韦斯特洛克](https://unsplash.com/@johnwestrock?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/waterfall?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄)

“设计、数学和框架”。

“你真的应该读读” [Pure UI](https://rauchg.com/2015/pure-ui) 和 [Pure UI Control](https://medium.com/@asolove/pure-ui-control-ac8d1be97a8d) 这两篇文章定义了一种应用程序开发(设计和创建)的方法，可以解析为这三个词。如果他们的数学方法稍微扩展到包括测试，那么我们可以说这三个词构成了一个全面而简单的瀑布开发过程。

[![3-word dev process flow](img/f764b6c9713903575d8a974a369925b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KNsMfCmW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/num9dfr6rq80d3bafnp1.png)

我发现这是一个有吸引力的简化，数学帮助开发人员的生活变得更容易，压力更小，对过程的依赖性更小。

*设计*定义外观以及核心状态和转换。 *Maths* 将状态和转换形式化，同时以可视化和协作的方式支持对缺失细节的调查，然后自动生成测试。应用程序*框架*只是实例化那些状态和转换。

## 纯 UI 设计

“纯 UI”的本质，正如本文开头的文章所描述的那样，是设计和业务逻辑之间的平滑交互。UI 实际上是业务逻辑或状态管理的副作用。提供这种明显魔力的解决方案是状态图。你使用哪个框架并不重要，只要它是功能性的和状态性的-> UI 是单向的！

## 什么是状态图，它们有什么帮助？

状态图是一个很好描述的数学概念(一个[介绍](https://statecharts.github.io/)和一些[资源](https://statecharts.github.io/resources.html))，它扩展了更简单的状态机。核心原则是状态的定义、状态之间的转换以及随后要采取的行动。可选的附加功能是状态管理和动作实现(我更喜欢让应用程序或 UI 框架来处理这些)。

状态图允许在不考虑实现细节的情况下对功能进行推理——因此更容易。特别是，状态图的[可视化](https://xstate.js.org/viz/?gist=61fb76fa71bd9216a21c70d1f241da91)使得与设计者的协作和功能的快速原型化(或更新)变得容易。链接的可视化来自下面“实施选项”中的应用程序示例，是该应用程序中的 [machines.js](https://github.com/johnkazer/hyperapp-xstate-demo/blob/xstate/src/machines.js) 文件的状态图的简单复制粘贴。因此，以这种方式设计的状态图可以直接在代码中使用。

## 用状态图进行自动化测试

当然，很多都是关于单元测试的。函数式编程天生适合状态机，React 或 Hyperapp 意味着单元测试相当简单。不太重要的是测试业务逻辑，或者当 UI 生成相关的事件序列时发生的功能和状态之间的交互。

如果提供状态图“路径”, Cypress 可以自动化业务逻辑测试，状态图“路径”代表自动生成的路线或转换序列。XState 有一个[图形模块](https://xstate.js.org/docs/packages/xstate-graph/#quick-start)来做这件事。

在可重用的函数中，只有不到 10 行简单的特定于应用程序的代码，这些代码驱动每个应用程序状态和有效“路径”的 [Cypress 测试](https://github.com/johnkazer/hyperapp-xstate-demo/blob/xstate/cypress/integration/tests.js)。你可以在这里观看测试视频【直播】[和 app 在这里](https://github.com/johnkazer/hyperapp-xstate-demo/blob/xstate/cypress/videos/tests.js.mp4)托管[(这是一个简单的媒体创建](https://picswithhyperappxstate.z33.web.core.windows.net/) [PWA](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps) ，它不会向任何地方发送数据，但会请求视频、音频和推送通知权限)。

## 维护

通过设计更新和状态图变化的逻辑结果，新的需求简单地从顶部过滤进来。原则上，测试将作为状态图的函数自动更新(实际上可能需要少量的手动输入)。

## 实现选项

关于数学和应用程序框架之间的具体责任边界，还有一些细节需要解决(例如，到底谁管理这个州)，但这些不会改变原则。

例如，在[这个例子](https://dev.to/johnkazer/use-maths-not-process-for-reliable-web-apps-1bmm)中， [Hyperapp](https://hyperapp.dev/) 框架负责管理状态和执行动作。然而在[中，这个](https://codesandbox.io/s/xstate-todomvc-33wr94qv1?from-embed)示例 [React](https://reactjs.org/) 很大程度上只是浏览器 DOM 的一个接口，而状态图(来自 [XState](https://xstate.js.org) )管理状态和动作执行。具体来说，在这种情况下，DOM 事件被链接到一个 [React 钩子](https://xstate.js.org/docs/packages/xstate-react/#api)，它触发一个状态转换——状态图完成剩下的工作。

一个非常有用的(但是写的很少？)状态图的特点是，它们可以自动提供应用程序用例作为状态转换的序列。这些序列，或者说路径，可以作为测试的基础——[这个例子](https://dev.to/johnkazer/use-maths-not-process-for-reliable-web-apps-1bmm)就是这样驱动[柏树](https://www.cypress.io/)的。这里的库是——使用‘x state’分支。

## 瀑布与数学

现在你有了它——一个比我记忆中更简单、更友好的瀑布开发过程。还有一些不吓人又有用的数学！

一个示例开发流程可能如下所示:

1.  使用 [Figma](https://www.figma.com/) 和 [sketch.systems](https://sketch.systems/ryanlucas/sketch/a58505b25ac114c6cec64922cb8fb325) 创建初始*设计*(使用[该 Figma 插件](https://github.com/johnkazer/Export-Figma-layer-IDs)并查看 sketch.systems 中的“导出到剪贴板”选项)，通知并被通知；

2.  [XState](https://xstate.js.org/) 中的状态图*数学*。

3.  通过*框架*的 UI(比如 [React](https://reactjs.org/) 或者 [Hyperapp](https://hyperapp.dev/) )是状态图行为的副作用。

4.  测试可以直接从状态图*数学*导出，用[柏树](https://www.cypress.io/)实现。

5.  需求变化可以很快融入到*设计*和状态图*数学*更新中，这(通常)需要简单的 UI *框架*调整。
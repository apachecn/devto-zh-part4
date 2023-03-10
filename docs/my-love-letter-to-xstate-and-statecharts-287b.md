# 我写给 XState 和 statecharts ♥的情书

> 原文：<https://dev.to/timdeschryver/my-love-letter-to-xstate-and-statecharts-287b>

在 Twitter 上关注我: [@tim_deschryver](https://twitter.com/tim_deschryver) |订阅[时事通讯](https://timdeschryver.dev/newsletter) |最初发表于 [timdeschryver.dev](https://timdeschryver.dev/posts/my-love-letter-to-xstate-and-statecharts) 。

* * *

XState 是由大卫·k·T2 创建的一个 JavaScript 库🎹。

> XState 是一个用于创建、解释和执行状态图的库。状态图是一种用于建模有状态、反应式系统的形式。计算机科学家 David Harel 在他 1987 年的论文 [Statecharts:复杂系统的可视化形式主义](https://www.inf.ed.ac.uk/teaching/courses/seoc/2005_2006/resources/statecharts.pdf)中提出了这种形式主义作为状态机的扩展。

我的 XState 之旅始于一年前，在看了 David Khourshid 的演讲[关于有限自动机的无限好的用户界面](https://www.youtube.com/watch?v=VU1NKX6Qkxc)之后。他的话使我有理由相信。真正打击我的是，我设计(UI 状态)的方式很糟糕，我自己把它弄得很复杂，因为我不知道更好。突出显示的例子是一个简单的 AJAX 请求，为了处理所有可能的场景，代码很快就变得复杂起来。大卫称之为自下而上(🍑🆙)方法。

在 XState 中吸引我的是它感觉很熟悉，而且作为一个很好的额外功能，我从多个演示中看到的代码是可读和易于理解的。起初，我把它看作是一个声明性的 redux 商店，一个门口有警卫的商店，以防止恶意类型进入商店，而不是一个门总是敞开的商店。
唯一可见的区别是没有还原剂。使用 XState(或一般的状态机)，状态通过转换来修改。想法略有不同，但结果不变，一个新的状态。现在，在一些概念证明之后，我想分享我喜欢 XState 的地方。

### 我为什么喜欢 XState

*   更难引入“无效”状态
*   考虑状态和状态转换是构建(部分)应用程序的第一步，它迫使你在编写代码之前考虑逻辑
*   创建 XState 机器是完全类型安全的
*   可以将一个状态机导出到一个状态可视化器中，在这里您可以在一个清晰的图像中看到所有的状态转换
*   状态机和状态图并不是什么新东西，这个概念在我出生之前就已经经过了考验
*   就像 redux 一样，状态是确定的
*   这些文档写得很好，可以搜索，所以很容易找到你要找的东西
*   这种逻辑可以被非开发人员理解和讨论

### 反思最初的想法

虽然这些例子很容易理解，但编写我的第一台机器却很难。因为很容易看到机器中发生了什么，不需要太多代码，以人类可读的方式，创造一个机器的复杂性是隐藏的。以这样一种声明性的方式编写代码对我来说是新的，需要一些时间来适应它。一旦语法熟悉了，写一个机器就变得容易多了，现在最花时间的是画出状态和转换的模型。

XState 与 redux 有些相似之处，但它们并不相同。XState 有多个较小的存储(Actors)来发送事件，而不是只有一个全局存储来分发所有的动作。这个架构被称为[演员模型](https://en.wikipedia.org/wiki/Actor_model)。参与者持有状态，可以接收消息并决定如何处理消息，向其他参与者发送消息，以及创建更多参与者。
另一个区别是，机器是非常明确的，你不可能意外地陷入糟糕的状态。而使用 redux 更容易发现自己处于无效状态。
我相信以前使用过 redux 架构，一定会帮助我开始。

一个很好的好处是机器是 UI 框架/库不可知的，它不绑定到特定的 UI 框架。在多个 UI 框架中重用同一台机器是可能的，区别在于视图层和用户如何与视图交互。所有的(业务)逻辑只需在机器中编写一次。它让你把 [UI 当作事后的想法](https://michel.codes/blogs/ui-as-an-afterthought)，这是我没有想到的。这就是为什么我想创建一个实验来创建一个机器，并将其与 Angular，React，Svelte 和 Vue 一起使用。表机包含表组件的逻辑。

### 演示项目: [xstate-table](https://github.com/timdeschryver/xstate-table)

桌面机的[状态可视化器](https://statecharts.github.io/xstate-viz/)如下图所示:

[![Image with the visualizer of the table transitions](img/b01ed0e1da3b95d310e690b452ff630d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RKGJKTpe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://timdeschryver.dev/posts/my-love-letter-to-xstate-and-statecharimg/xstate-visualizer.png)

在图片的左侧，可以看到状态逻辑。工作台可以处于`idle`状态和`dragging`状态。
当表格处于`idle`状态时，有点击动作，其中一些动作有一个 guard 子句。例如，`ctrlClick`动作只有在满足`isCtrlClick`条件时才会被触发。“正常”点击动作只有在不满足其他点击保护时才会被触发。

还有一个`mousedown`动作，它将状态从`idle`转换到`dragging`。当 table machine 处于`dragging`状态时，`mousemove`将管理被选择的行，并使用新的坐标触发选择框的新渲染。

在图片的右侧，可以看到桌机的选择状态。每一个`click`、`mouseup`、`mousemove`动作都会产生一个新的状态:`SINGLE_SELECTION`、`MULTI_SELECTION`或`EMPTY_SELECTION`。

在代码中，我们可以使用这些状态来显示选择框或禁用按钮。

代码可以在 [GitHub](https://github.com/timdeschryver/xstate-table) 上找到，或者在以下沙箱中找到:

*   [角度](https://stackblitz.com/edit/xstate-angular-table)
*   [反应](https://codesandbox.io/s/1dtmk)
*   [苗条的](https://codesandbox.io/s/yb6lq)
*   视图

### 关于演示项目的想法

这是一个简单的实验，但我对结果很满意。一旦机器被编写出来，跨不同的框架实现机器就很容易了，即使我并不精通所有的框架。文档中有一节介绍了如何在框架内使用机器，这些示例提供了入门指南。对于 React，甚至有一个`@xstate/react`库，它有一个`useMachine`钩子，可以完成所有繁重的工作。基于`useMachine`，我创建了一个苗条的商店。对于 Angular (RxJS)和 Vue，文档提供了一个示例。

在我的实验中，桌面机是 100%可重用的，所以我称这个实验是成功的。我在每个框架中改变的一点是如何选择表格行(用于确定行是否在选择框中)。使用`document.querySelectorAll`的默认实现完成了它的工作，但是每个框架都有自己的方式来`ref`一个 HTML 元素。我不知道为什么我想使用框架的实现，我只是想知道我是否可以。

我对代码很满意，如果我几个月后再来看它，我肯定会到处修改它，但重要的是它要易读。

### 结尾词

XState 不是 redux 的替代品，我还是喜欢我的 redux。它们都满足不同的需求。我认为 XState 是放置组件逻辑的完美地方。如果我必须创建相同的表组件，但没有 XState，我会得到一些难以理解的混乱代码。

有一个学习曲线，但我希望您在一次性项目中，甚至在生产应用程序中尝试一下 XState 和 statecharts。到目前为止，我只是用它来熟悉它，但我期待在生产项目中使用它。即使没有在生产项目中使用它，我确信我的代码已经改进了，只是因为我开始从不同的角度看待问题和状态。

与命令式代码相比，设置状态机的初始成本可能会更长。从长远来看，当添加了更多的特性并且(最后一分钟)需要更改时，状态机为您编写和维护代码提供了坚实的基础。

对我来说，使用 XState 或 statecharts 最重要的一点是它是声明性的和显式的，很难创建无效的状态。它带来的好处是围绕它的工具和跨框架的可移植性。

### 更多资源

*   [x 状态文档](https://xstate.js.org/)
*   [埃里克·莫根森——欢迎来到状态图的世界](https://statecharts.github.io/)
*   [David Khourshid -反应式状态机和状态图|上坡会议 2019](https://www.youtube.com/watch?v=GSHQFx7PG20)
*   [David Khourshid -用有限自动机简化复杂的 ui&state charts | JSConf 冰岛 2018](https://www.youtube.com/watch?v=RqTxtOXcv8Y)
*   [David Khourshid - Reactive 状态机和状态图| ReactiveConf 2018](https://www.youtube.com/watch?v=DrHccvns-L0)
*   [Shawn McKay -使用 XState v4 | React Van 绘制应用程序](https://www.youtube.com/watch?v=jw03YmNffks)
*   和 Jason 一起学习-让我们和 David K. Piano 一起学习状态机！

* * *

在 Twitter 上关注我: [@tim_deschryver](https://twitter.com/tim_deschryver) |订阅[时事通讯](https://timdeschryver.dev/newsletter) |最初发表于 [timdeschryver.dev](https://timdeschryver.dev/posts/my-love-letter-to-xstate-and-statecharts) 。
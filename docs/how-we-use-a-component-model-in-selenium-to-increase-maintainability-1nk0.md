# 在 Selenium 中使用组件模型来提高可维护性和可重用性

> 原文：<https://dev.to/ddaypunk/how-we-use-a-component-model-in-selenium-to-increase-maintainability-1nk0>

在 Sprout Social 存在的 9 年里，我们的 [Selenium](https://www.seleniumhq.org/) 框架已经成长为数百个场景，拥有同样多的页面类和工厂容器。我们注意到管理变得很麻烦，因此，我们的 QA 工程师认为正在使用的模型需要更新。所以最近，我们采用了我们喜欢称之为**的组件模型**。

*注意:所有代码示例都可以在下面的 [Github Gist](https://gist.github.com/ddaypunk/4f3e62d4b049cf2aa460e3155e2aa099) 中找到。*

这个组件模型的想法是几件事情的结果:

*   现代网站不再是严格意义上的静态“页面”
*   鲍勃叔叔关于坚实原则的成长@萌芽演讲
*   我们的 web 应用程序使用了 [React.js](https://reactjs.org/) ，它使用了类似组件的概念进行封装

现代网站(即 web 应用程序)是高度动态的软件，具有全局栏、侧栏、内容视图和弹出窗口。这些碎片中有许多是由其他更小的碎片组成的。考虑到这一点和坚实的原则，我们意识到页面对象模型创建的整体类不再对我们有利。

我们注意到我们有多个功能区，但实际上只有一个页面内容的位置。我们有一个全球顶级导航栏，也包括设置访问。此外，左侧还有一个辅助导航条，用于在功能区域内导航。许多页面在右侧还包含一个导轨，通常用于放置内容视图的日期选择器和过滤器。请参考下面的模型，以帮助形象化这一点:

[![](img/2e913f94d62c6893a8a05c0971069898.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KT8toHUC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s37jtenw51dgmeq8849x.png)

我们注意到框架中有很多地方我们可以[干燥(不要重复)](https://www.codeproject.com/Articles/36712/SOLID-and-DRY)代码。这包含在坚实的软件开发原则中。按钮、输入和数据可视化都共享 UI 实现中的公共代码。那么，为什么我们的测试要跨多个页面类实现多种方法来获取过滤器并与之交互呢？

相反，我们同意我们的 selenium 测试使用的组件应该是它们的等价 web UI 组件的 1:1 表示。以这个 [CheckboxV1](https://gist.github.com/ddaypunk/4f3e62d4b049cf2aa460e3155e2aa099#file-checkboxv1-java) (点击查看要点代码片段)类为例。该模型最有益的方面是:组件包含了其独特的 selenium 选择器，以及您的测试可以使用的特定构造函数和方法。这大大增强了代码的可读性和框架的可维护性。

使用这些组件一段时间后，我们开始注意到代码在类之间重复。为了*消除*重复，我们创建了一个名为 [BaseComponent](https://gist.github.com/ddaypunk/4f3e62d4b049cf2aa460e3155e2aa099?ts=4#file-basecomponent-java) 的类来控制任何组件的大部分基本行为。然后，可以从这个 BaseComponent 扩展所有其他组件，以确保每个组件的实现尽可能简单。

将我们最初的 **CheckboxV1** 的实现与从 BaseComponent 继承的**结合起来，进一步简化了组件，增加了框架的可维护性。[复选框 V2](https://gist.github.com/ddaypunk/4f3e62d4b049cf2aa460e3155e2aa099?ts=4#file-checkboxv2-java) 允许 BaseComponent 完成大部分非特定组件的繁重工作。我们在修复问题时也少了一个需要检查的地方，因为大多数行为都发生在 BaseComponent 中，而不是分散在多个组件中的相似功能的多个版本。**

关于上面的 **CheckboxV2** 实现，我们倾向于对 UI 代码中有属性的组件使用字符串参数构造函数。 *By* 和 *WebElement* 参数构造器是我们 UI 中元素的备份，这些元素可能不包含特殊的数据属性，我们称之为 QA 属性，但遵循 DOM 中相同/相似的组件结构。然而，我们鼓励我们的团队添加他们自己的 QA 选择器，如果能够的话，或者进一步与开发人员合作来添加它们。拥有它们通常会使组件代码和后续的 Selenium 自动化总体上更加简单。

这里有一个简单的例子，展示了我们如何在一个[页面](https://gist.github.com/ddaypunk/4f3e62d4b049cf2aa460e3155e2aa099?ts=4#file-page-java) **和** [步骤定义](https://gist.github.com/ddaypunk/4f3e62d4b049cf2aa460e3155e2aa099?ts=4#file-checkboxsteps-java)类中使用组件。

通过对我们的框架进行多次重构，这是一次有趣的旅程。然而，它一次又一次地证明了让我们的框架变得更容易开发和维护。我们已经开始考虑进一步改进的方法，比如使用一个名为*选择器*的包装类来帮助格式化 BaseComponent，并使其更加友好。我们期待着在未来提供更多的信息

您或您的组织是否使用过或目前正在使用类似的东西？请在评论中分享你的经历！
# 为 SwiftUI、Combine、iPadOS、Project Catalyst 和 iOS 行业的任何其他飞跃准备您的应用程序的 3 个步骤

> 原文：<https://dev.to/essentialdeveloper/3-steps-to-prepare-your-apps-for-swiftui-combine-ipados-project-catalyst-and-any-other-leaps-in-the-ios-industry-544k>

在今年的 WWDC 上，苹果公司发布了新的框架和平台，如 SwiftUI、Combine、iPadOS、Project Catalyst 和许多其他精彩的更新，让我们这些开发人员大吃一惊。

再一次，iOS 开发者正在寻找一个绝佳的机会，通过掌握新技术并在 iOS 就业市场上提供卓越的价值，在游戏中取得领先。

然而，对于许多 iOS 开发者来说，这一消息意味着他们必须经历巨大的范式转变，才能跟上新的行业趋势。

就像 Swift 推出时一样，我们注意到许多开发人员害怕错过这个绝佳的机会。

当然，作为重要开发人员社区的一部分，我们不希望您被落下。因此，在这篇文章中，您将了解采取什么样的最终策略:

1.  为即将到来的迁移准备代码库，以利用所有新的平台和框架，如 SwiftUI
2.  通过将组件分为平台无关的和平台特定的，扩展您支持的平台
3.  通过我们行业的结构性变化(如语言和范式转变)而蓬勃发展

## 新框架和平台的迁移挑战

尽管探索 SwiftUI 在无需长期维护的 pet/测试项目上的功能是有趣且有教育意义的，但在支持商业产品的 iOS 代码基础上采用新的变化需要付出更多的努力和承担更多的风险。

对于创建并将 UI 和其他框架(尤其是第三方)视为可以插入业务逻辑的独立框架的 iOS 开发人员来说，向 SwiftUI 等新技术的*迁移将是简单、快速和非常低成本的*。这是因为业务逻辑组件和应用程序的 UI 代码之间的耦合度很低。例如，使用解耦逻辑，开发人员只需用新的 SwiftUI 实现替换当前的 UIKit 模块(业务逻辑保持不变！).

[![](img/72cc271c013cea247f9a387ef4356f4d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lXzVDF_Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zc21y9uv36sjasr47695.png)

另一方面，为了采用新的 API，必须重写业务逻辑和 UI 之间高度耦合的代码库(或者重写部分代码)。在这种情况下，迁移到 SwiftUI 的成本会高得多。

[![](img/81eae1215fb4a4b5a446d995043ffb44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bwC2t7b6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bouw7qk1r9ei6dtbeboj.png)

将业务逻辑从一个 UI 框架迁移到另一个 UI 框架是一个危险而缓慢的过程，有可能破坏现有的特性和业务逻辑，因为 UI 和核心逻辑之间没有明确的分离。在这种情况下，许多团队可能会正确地决定根本不迁移。

此外，iOS 团队将不得不将其重点转移到重建现有的功能，以适应新的 UI 模式，而不是将精力放在扩展应用功能和丰富产品的 T2 上。此外，通过迁移现有功能，用新的 UI 组件测试应用程序的行为很可能要从头开始。

将核心逻辑从一个 UI 框架迁移到另一个 UI 框架的更好的方法是开始从 UIKit 或任何其他 UI 框架中提取和分离业务逻辑。

SwiftUI 是 iOS13+，由于大多数公司支持至少两个以前的 iOS 版本，这给了你足够的时间(大约 2 年)来准备你的代码库，以便顺利迁移。

准备迁移到 SwiftUI 和新框架及平台的第一步是在您的应用层之间建立低耦合(模块化方法)。

## 平台无关组件与平台相关组件

旨在通过*随时间的迭代重构*将 UI 组件与业务逻辑组件隔离开来。换句话说，在快速可靠的测试套件的指导下，努力进行小批量的工作(频繁的小提交和合并)。

通过将业务逻辑从 UI 组件中分离出来，您就可以像对待任何其他框架一样对待 UI，一个“即插即用”的解决方案。然后，您可以在主 iOS 应用程序中合成所有模块(合成根)。

[![](img/45f0bff4a4ffd6027616269dcb049499.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c0xZa8z4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/toz8swyk4dp1g8mlypbt.png)

模块之间的分离和同质性有助于您的团队和代码库适应我们在过去几年中经历的大范式转变(未来未知！).结果，甚至改变整个层的成本，比如应用程序的 UI，都变得非常小。

例如，添加带有 SwiftUI 实现的 watchOS 应用程序将会快速而简单，因为您可以重用域逻辑:

[![](img/922b5fc074ba4dc94fc85a5bfa87d980.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--elrLv_wK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ai54etxizsmbiydr7c96.png)

通过努力在代码库模块中实现隔离和一致性，您还可以看到以下非常受欢迎的副作用:

*   开发和测试时间更短
*   iOS 团队的幸福感和成就感增加
*   更顺畅的运营会带来更好的结果，并随之带来更快的职业发展
*   更快的上市时间(和适应性)
*   减少 iOS 团队内部的混乱和沟通不畅，尤其是新加入的团队
*   对整个系统的状态进行更加可控的管理
*   更少的错误和缺陷
*   新技术/需求/实验的风险更小，因为它们变得孤立且易于替换

将您的代码组织到隔离的同构模块中的另一个好处是平台无关的业务逻辑组件和平台特定的组件的分离。将您的 iOS 应用程序迁移到 SwiftUI 只是以不同的方式编写您的应用程序:

[![](img/e83832efe6dc142c18d08089ff44ede1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sfuWCoLM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3vx578v6yi0q5o217lw0.png)

您甚至可以开始向您的客户提供更好的 SwiftUI 体验，同时通过根据 iOS 版本对您的应用程序进行不同的组合来支持旧 iOS 版本:

[![](img/41b05433606228ecdc2e8a69470bc808.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---uN2PEFv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2ifemvw6v2dd2ycors6t.png)

*将组件分为平台无关的和平台特定的，使我们能够重用代码库的重要部分，以部署到多个平台。*

您可以用原始的 Swift 类型(类、结构、枚举)来表示业务逻辑，这使得它与平台无关。相同的业务逻辑组件可以用于不同的部署平台，这取决于您想要插入它们的 UI。换句话说，你最终可以拥有相同的应用程序“大脑”(业务逻辑组件)，但部署在 iOS、watchOS、tvOS、iPadOS、macOS、Linux 甚至 web 上。

您甚至可以在需要时将 UI 从域模块中分离出来(例如，在具有不同域逻辑的独立应用程序中重用 UI 元素)。

Xcode 11 使得将代码提取到可以共享的独立平台无关的框架和包中变得极其容易。

**准备迁移到 SwiftUI 和新框架及平台的第二步是将组件提取到解耦的框架和包(模块)中。**

## 在 iOS 行业的结构变化中蓬勃发展

实现模块化使您能够更直接地应对变化，即使在整个行业必须转变并采用新标准的罕见和不可预测的事件中。

模块化设计降低了适应变化的成本和时间，无论这种变化是来自公司内部的产品需求，还是来自外部因素，如苹果公司的公告。

为行业的重大转变做准备的第三步也是最后一步是，在实现新特性时，保持框架和包之间的低耦合。

作为专业的 iOS 开发人员，我们编写软件来解决问题，改善客户的生活。尽管采用最新技术可能很有吸引力，但我们需要记住，我们对客户负有责任，首先要努力为他们提供最佳体验。

平稳的迁移(由模块化支持！)在帮助你采用新技术和新功能方面大有帮助，这些新技术和新功能让你的应用程序变得很棒，具有**高速度、低成本、**和**信心**。
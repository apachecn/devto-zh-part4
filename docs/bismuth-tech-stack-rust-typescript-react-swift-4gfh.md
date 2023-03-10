# 我们的技术堆栈:Rust、Typescript、React 和 Swift

> 原文：<https://dev.to/bismuthlabs/bismuth-tech-stack-rust-typescript-react-swift-4gfh>

# TL；博士；医生

*   生锈(actix-web，Diesel)
*   Swift (SwiftPM，swift-ast)
*   以打字打的文件
*   反应(Atlaskit，Redux)
*   AWS (Fargate，PostgreSQL RDS)
*   将（行星）地球化（以适合人类居住）

# 公司文化

我有一个坦白:我喜欢讨论技术栈。它们传达价值观，并能告诉我们很多关于公司文化和技术团队的技术历史。

当然，他们并不总是讲述整个故事:我知道一个强调员工幸福的地方，在那里 COBOL 仍然是王者。

能够解决你所有问题的*圣*栈并不存在。没有[银弹](http://faculty.salisbury.edu/~xswang/Research/Papers/SERelated/no-silver-bullet.pdf)，记得吗？相反，您可以选择对您来说更有意义的堆栈，您觉得合适的堆栈，适合您的用例的堆栈。

在最新技术和小众语言上全押可能很有诱惑力。当我开始我的职业生涯时，有人明智地告诉我，一个新的企业不应该有超过一个新的闪亮的东西。我相信这是真的，开发人员有时会被无法熬过下一个冬天的技术所吸引。

# 铋

铋是一种服务，它获取你的代码，通过磨房，告诉你哪里的结构不理想。我们仍处于早期阶段，有数百个想法，但最终目标是改善软件开发过程。不仅是开发商，还有[其他利益相关者](https://dev.to/bismuthlabs/lost-in-translation-cooperation-with-technical-people-18nn)。

我们目前专注于移动应用，但工具和理念可以应用于任何东西。

[![](img/b3fb6f41cdfef349f71de79e3f171420.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WOp8Qz89--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v01f5hfhxrj32a99w8tz.png)

# 科技栈

铋有四种主要成分:

*   我们支持的语言解析器，
*   API 的 REST 端点，
*   处理大量代码的工人，
*   用于用户交互的可视化引擎。

## 解析器

解析源代码可能具有挑战性，但这是一个已经解决的练习。我们的座右铭是不要为每种编程语言重新发明轮子，也不要推出我们自己的解析器。每个环境都有自己的一套工具。我们很可能不会比他们更聪明。

对于 swift，我们用的是 [swift-ast](https://github.com/yanagiba/swift-ast) ，恰好是用 Swift 写的。还有其他选择，如使用 C++ Swift 编译器前端，但我们的团队大多熟悉 Swift，该库提供了我们需要的一切。

对于 Java 和 Kotlin，我们可能会选择 Kotlin 的 Jetbrains UAST。

对于 Javascript 和 Typescript，有更多的选项:[Esprima](https://esprima.org/)(JS)[RESSA](https://github.com/FreeMasen/RESSA)(Rust)，以及 Typescript 编译器本身。

## API 终点&工人

我们选择了[锈](https://www.rust-lang.org/)。对于 web API 来说，这听起来可能令人惊讶:动态语言在这种类型的工作中有很好的声誉，但是 Rust 包含了我们所相信的价值观:

*   正确性:铁锈有很强的倾向让你以正确的方式做事。Rust 库在没有真正投入生产的情况下就发布了 1.0 版本，这种情况非常罕见吗？

*   稳定性:Rust 团队非常小心，不会在两次发布之间破坏东西。这并不意味着他们进展不快(两次发布之间大约 6 周)

*   速度:这对我们来说不太重要，但是 Rust 非常快，即使与 C++或 C 相比。

*   **图书馆&建造**:在 [crates.io](https://crates.io/) 生态系统中有成吨的图书馆可以使用。如果你用过 Ruby Gems、PHP Composer 或 Gradle dependencies，你会觉得构建工具 [cargo](https://doc.rust-lang.org/cargo/) 很熟悉。

*   所有这些价值观都反映在**社区**中。它是有帮助的，包容的，新手也有人照顾。

虽然 Rust 还相对年轻，但它有很多特点。软件是很难的，拥有一门促进良好实践的语言就像一个朋友握着你的手。

与破解 Ruby 或 JS 脚本相比，编写 Rust 代码通常会花费我们更多的时间，但 Rust 在编译代码时给人的感觉是，它将是正确的，并且经得起时间的考验。就我个人而言，这是我有生以来从未有过的感觉。

我们相信在雇佣新的开发者时使用 Rust 也是一个优势。Rust 开发人员通常对干净的代码很敏感:他们的大脑已经被显式的错误处理和非常严格的编译器洗过了。

他们可能比 Java 开发人员更难找到，但这可以被视为一种优势，因为 Rust 吸引优秀开发人员的几率可能比 Java 更高(这里是火焰战争的燃料)。

## UI 和可视化

[Typescript](http://www.typescriptlang.org/)/[React](https://reactjs.org/)/[Redux](https://redux.js.org/)如今已经是当年前端的标准栈之一。除非 4-5 年前 JS 前端战争非常激烈，每周都有新的竞争对手，否则我们认为赢家已经出现，我们不会在未来几年看到太多的颠覆(即 React、Angular 和 Vue.js 已经获胜)。

我们的可视化是用 [D3](https://d3js.org/) 构建的，这是在网络上显示可视化数据的事实标准，我们的 GUI 是用 [Atlaskit](https://atlaskit.atlassian.com/) 构建的。

我们在网络和我们的[电子](https://electronjs.org/)应用上都使用这个前端堆栈。电子有时真的令人沮丧，但它在桌面上正变得无处不在(如果上帝存在，当她看到我们如何浪费内存时，她会毁灭人类)。

## 基础设施

所有这些代码都放在一个 monorepo 中。这有助于在一个地方跟踪所有内容。从负面来看，这使得 CI 变得更加困难，因为您必须检测应用程序的哪个部分受到了影响。这可能会通过使用 Bazel 来构建我们系统的每个组件而得到缓解，但我们还没有做到这一点。

我们使用 [Terraform](https://www.terraform.io/) 在 AWS 上部署。我们没有 AWS 或 Terraform 的经验，但文档足够好，很容易找到支持。这年头谁的圈子里没有懂 AWS 的人？

我们使用 [RabbitMQ](https://www.rabbitmq.com/) 在所有组件之间传递消息。我们*离达到极限还很远，到目前为止这对我们很好。*

## 未来

我们还不知道未来会是什么样子，但我们相信我们的堆栈足够坚固，可以承受它。时间会证明一切；)
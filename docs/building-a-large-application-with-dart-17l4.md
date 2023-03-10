# 用 Dart 构建大型应用程序

> 原文：<https://dev.to/zoebourque/building-a-large-application-with-dart-17l4>

Quire 不是第一个用 Dart 编写的 web 应用，也不会是最后一个，但它可能是第一个如此依赖 Dart 的应用，无论是客户端还是服务器端。

[![Dart](img/3832c31bde7d2c75a1d18f97916490c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BfC1BiUF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bxlted37px1wjujzfrzs.png)

这是一个有趣的任务管理器，核心是树形结构。该项目本身有 53992 行代码，1620KB 的 Dart 代码，是用社区中的一些开源库构建的。

# 我们是谁？

在我们开始从事这个项目之前，我们是被称为 Rikulo 团队的 Dart 爱好者，他们已经发布了几个 Dart 库，包括 UI 框架、UI 库、web 服务器、消息服务器、数据库客户端等。

当 Dart 在 2011 年首次发布时，我们都对 Dart 的未来价值感到兴奋，并立即开始在小部分项目中与 Dart 合作。最终，我们继续构建一个主要由 Dart 组成的完整应用程序。在这里，我们想谦虚地分享我们的一些经验，希望给你一些关于在 Dart 开发的信息。

# 选择镖

选择 Dart 的理由有很多。其中，对我们来说最重要的是:

*   Dart 的强类型系统保护我们免于无数微小的错误。官方 Dart Editor 提供了对类型错误和自动完成的快速反馈，以及更简单的跟踪。
*   我们认为基于类的继承模型比基于原型的模型更直观。
*   Dart 提供了在客户端和服务器端使用相同语言的机会，共享相同的数据模型和代码库。
*   Dart 修复了 JavaScript 中的大多数问题。Dart 在这方面并不完美，但它确实解决了 99%的老问题。
*   Dart 有几个现在流行的 JavaScript Harmony 特性:Future (promise)、Arrow 函数等。
*   Dart 有强大的团队做后盾，有很多高质量的官方库和组织良好的 API。Dart 仍在发展，但规范已经相当稳定。
*   在服务器端，我们更喜欢事件循环模型而不是多线程。
*   Dart 在编译成 JavaScript 时会执行树抖动。(见下文。)

选择 Dart 也有缺点:

*   Dart 社区比 JavaScript 社区小得多。
*   Dart 和 JavaScript 之间的通信并不简单。
*   用强类型语言填充 API 更加困难。
*   Dart 编辑器的性能对于大型项目来说还不是最佳的。

在强大的类型支持下，用 Dart 编写比用 JavaScript 要健壮得多。此外，Dart 比 Java 更简洁，有时(例如，使用函数表达式)甚至比普通的 JavaScript 更简洁。总的来说，使用 Dart 很舒服，除了几件事:

*   Final 字段必须在构造函数体之前的初始值设定项中初始化。
*   Mixin 规范比较不成熟，2.0 之前不会修复。
*   函数声明没有泛型类型参数，只在类上有。(当然，这样会增加编译器的工作量。)

## 客户端

在 Dart VM 登陆 Chrome 之前，你会期望通过编译成 JavaScript 来交付你的产品。有很多语言可以编译成 JavaScript，但是 Dart 有一些额外的优势。

*   在开发中，我们在 Dartium 上本地运行 Dart，Dart ium 是一个从 Chormium 派生出来的浏览器，内置 Dart VM。在这次迭代中我们不需要编译，免除了我们无数次的中断。
*   在测试和生产中，我们将它们编译成 JavaScript 并在所有主流浏览器上运行。Dart-to-JavaScript 编译器执行树抖动，从结果中删除未使用的代码。这大大减少了我们的 JavaScript 代码大小。

## 服务器端

服务器端的 Dart 从来不是 Dart 社区的热门话题，但是我们认为 Dart 是服务器端编程语言的有力候选，因为:

*   Web 服务由于其异步的本质，与事件循环模型(通过多线程)配合得非常好。
*   服务器端代码比客户端要求更高的健壮性和安全性。在这方面，强类型语言确实很方便。

# 支持库

Quire 由大约 30 个库导入组成，其中 10 个来自社区，其余的由 Dart 团队发布。如果你是飞镖专家，你可能会猜测有 AngularDART 和 Polymer.dart，但实际上它们都不存在。

我们没有使用 [AngularDART](https://angulardart.org/) ，因为:

*   我们想要精致的 DOM 控件。
*   我们使用独特的架构指导方针来设计客户端结构，范式不同于角度逻辑。
*   当我们调查 AngularDART 时，它对编译的 JavaScript 代码大小产生了很大的开销，但这已经得到了很大的改善。

我们也没有使用 [Polymer.dart](https://www.dartlang.org/polymer/) ，因为:

*   由于封装和事件重定向，ShadowDOM 不能与面向选择器的框架协作，例如 Bootstrap。
*   你不能从 ShadowDOM 的外部注入风格。如果第三方组件集是由聚合物制成的，用户几乎不可能改变它的外观和感觉。更新:自 2013 年 12 月起，您可以从外部修改内部样式。参见[暗影 DOM 201](http://www.html5rocks.com/en/tutorials/webcomponents/shadowdom-201/#toc-style-cat-hat) 。

## [T1】DQuery&boot jack](#dquery-amp-bootjack)

我们客户端堆栈的基石是 DQuery 和 Bootjack，它们都是由 Rikulo 团队发布的开源项目:

*   [DQuery](https://github.com/rikulo/dquery) 是 jQuery 到 Dart 的部分移植，重点是 jQuery 的事件委托系统。
*   [Bootjack](https://github.com/rikulo/bootjack) 是 Bootstrap 3 的完全移植，CSS 和 API 几乎完全相同。

我们以这种方式构建了我们的应用程序栈，以利用来自 JavaScript 世界的知识和技能。

## 流

[Stream](http://www.rikulo.org/projects/stream) 是我们的 web 服务器，纯 Dart 编写，具备路由、过滤、服务器端 MVC 等能力。Stream 与事件循环模型无缝协作。编写请求处理程序就是将一系列非阻塞例程链接起来。与传统的多线程模式相比，它不仅效率更高，而且工作起来也更愉快。我们还扩展了我们的 web 服务，用 nginx 处理 HTTPS，并将请求委托给流。借助这种体系结构，我们能够单独生成和放弃 Dart 虚拟机，并在不中断用户活动的情况下升级服务器。

# 结论

我们与 Dart 有着愉快的合作经历，我们期待着 Dart 社区有更多令人激动的发展。最后但同样重要的是，如果你对 Dart 能实现什么感兴趣，来玩玩我们的[项目管理软件](https://quire.io/compare/best-project-management-software-reviews-comparisons)、 [Quire](https://quire.io/) 。免费的！
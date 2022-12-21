# Node.js 框架:怀疑、问题和另一个框架

> 原文：<https://dev.to/ktutnik/node-js-framework-scepticism-problems-and-yet-another-framework-1edf>

## 怀疑主义

当有人向公众发布新的 TypeScript 框架时，我看到了一些不同的回应，其中一些是积极的反馈，但大多数顶级评论都是消极的回应，例如:

看起来{Nest，LoopBack}上已经有了所有功能，为什么不考虑为它们做点贡献呢？

*我不需要一个服务器端 TypeScript 的框架，我可以在不到 200 行代码内编写自己花哨修饰的框架。*

以至

祝贺你，你刚刚把 JavaScript 变成了一个狗屎版本的 Java。

最后一个很粗鲁，但不能完全不同意。自从早期版本的 TypeScript 语言以来，基于 TypeScript 的最成功的框架是 Angular 2。这是一个很棒的 UI 框架，有很强的观点，里面有很多 Java 风格的最佳实践。它带有内置的依赖注入和基于装饰的配置。Angular 2 的成功故事启发了其他 TypeScript 框架(UI 和服务器)使用类似的功能。

这一事实使得 TypeScript 氛围更像 Java，而不像 JavaScript 那样灵活。静态类型语言的人可能会喜欢它，但是经常使用动态类型语言的人，尤其是 JavaScript 用户会给出相反的意见。

## 问题

我完全同意你可以用不到 200 行代码，用基于 decorator 的配置来编写你自己的类型脚本框架。我甚至亲自证明了。我用 175 行代码创建了一个小小的框架,包含基于装饰的路由、简单的参数绑定和可选的依赖注入。

但是，我怀疑你能否用上面的框架轻松地编写一个健壮安全的应用程序。即使您可以，您也没有最佳地使用 TypeScript 的功能。我相信你最终会创建一个用 JavaScript 很好实现的应用程序的冗长版本。例如，您不断创建模式来为您的域对象指定数据类型，例如用于验证的 Mongoose 模式和 Joi 模式，同时不断为域模型创建另一个类或接口以使其类型安全(以使编译器和 IDE 满意)。

启用时，TypeScript 能够在运行时提取元数据(反射)。有了合适的反射库，框架可以正确理解数据类型。此功能可用于在后台创建模式，并将其用于进一步的处理，如验证、数据转换等。

## 还有另一个框架

[Plumier](https://github.com/plumier/plumier) 是一个新的 Node.js 框架使用 TypeScript 作为它的主要开发语言。基于上述问题构建的 Plumier。它的目的是让使用 TypeScript 的开发时间变得愉快。

Plumier 使用专用的反射库从头开始构建，因此它可以最佳地使用 TypeScript 的功能。它主要使用丰富的元编程特性，例如:

*   使用代码约定生成的路线。
*   无需架构配置的验证和类型转换。
*   静态路由分析。
*   声明式授权。
*   改进的中间件管道支持异步/等待。
*   快速轻便。
*   未出版。

如果这篇文章没有消除你的怀疑，你可以阅读下面关于框架是如何建立的完整故事。

[![I Ketut Sandiarsa](img/72a3d869c4ad4889736dd218b3bb0fd4.png)](https://hackernoon.com/i-spent-a-year-to-reinvent-a-node-js-framework-b3b0b1602ad5) [## 我花了一年时间重新发明了一个 Node.js 框架|作者:赖克特·桑迪亚萨| HackerNoon.com | Medium

### 我赖爷桑迪亚萨<time datetime="2019-05-28T03:16:01.033Z">2019 年 5 月 28 日</time>11 分钟念T3】hackernoon.com](https://hackernoon.com/i-spent-a-year-to-reinvent-a-node-js-framework-b3b0b1602ad5) 

## 支持项目

构建一个框架最难的部分是建立和维护一个稳固的社区，如果你认为 Plumier 符合你的需要，请支持 [GitHub](https://github.com/plumier/plumier) 上的项目，帮助 Plumier 创造一个更美好的未来。
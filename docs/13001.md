# 全堆叠角度显影器

> 原文：<https://dev.to/cogoo/the-fullstack-angular-developer-ihg>

TLDR:Angular 和 NestJS 应用程序之间的相似之处是惊人的。您可以在两个平台之间共享类型、函数等，允许您，一个 Angular 开发人员，使用 Angular 范例编写一个全功能的后端应用程序。

我对“全栈”这个术语的想法牢牢地系在一个钟摆上。目前在“没有这回事”的区域内摇摆。每一个学科都过于宽泛，无法对两者都有深入的了解。你也许可以用 Express 启动一个 API 服务器，但这并不能让你成为“全栈”。

[![](img/57d96dde84ba4e09fd7ef49a0b04503e.png)](https://i.giphy.com/media/a93jwI0wkWTQs/giphy.gif)

尽管如此，术语 Fullstack 在我们的行业中是存在的，并且我已经看到了许多有 React + NodeJS 配对的招聘信息。这让我想到了角度生态系统中的堆栈是什么。看哪，内斯特。

我们最近不得不构建一个需要一些 Restful APIs 的特性。为了不被挡住，我们走下了“更容易”的路🤨用 NodeJS 创造我们自己的路线。我们已经在使用 Nx 来管理我们的 monorepo，所以向我们的项目中添加 NestJS 是微不足道的。

学习曲线几乎为零。NestJS 深受 Angular 的启发，让您有宾至如归的感觉。类型脚本、装饰器、服务、模块等等都是 Nest 的特色。在创建 Restful API 时，它在抽象一些关键概念方面做得非常好。HTTP 响应代码会自动返回。Get、Post 方法和大多数其他增强的功能都是由 decorators 处理的。

当学习一个新的框架或库时，我发现最大的障碍是文档和可用资源的缺乏。谢天谢地，这不是 NestJS 的问题。文档很漂亮，由于 Nest 的流行，有大量有用的资源。除此之外，还有 Angular CLI 的原理图，所以搭建是轻而易举的事情。

最终，我看到 Angular + Nest 堆栈成为全堆栈 Angular 应用的事实。棱角分明的开发人员进入门槛低，这使它成为一个梦想的配对。
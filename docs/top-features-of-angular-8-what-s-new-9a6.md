# Angular 8 的顶级功能:有什么新功能？

> 原文：<https://dev.to/decipherzonesoft/top-features-of-angular-8-what-s-new-9a6>

2019 年第一季度，谷歌推出了备受社区期待的 Angular 8，人们对 Angular 8 的期望非常高，因为最初有人说 Angular 10 将是该框架的最终版本。大肆宣传要求 Angular 8 有巨大的性能改进，这与主要关注工具链非常匹配，也使 Angular 更容易为用户创建不同类型的应用程序以及其他性能改进。

***文章来源:[https://www . decipherzone . com/blog-detail/What-the-new-features-in-Angular-8](https://www.decipherzone.com/blog-detail/What-are-the-new-features-in-Angular-8)*T5】**

# ***棱角分明的 8 有什么新鲜事？***

通过向后兼容和支持，这个版本证实了新的 Angular 8 版本更轻、更快、更容易。现在 Angular 8 支持 TypeScript 版本 3.4。因此，在新的 TypeScript 版本的帮助下，使用 incremental 标志、全局 this 的类型检查和泛型类型参数进行更快的后续构建是非常容易的。

阅读:[为什么 Angular 更适合 Web 应用开发？](https://www.decipherzone.com/blog-detail/Why-Angular-is-Better-For-Web-Application-Development--)

**数据库支持**

在 Angular 8 中，谷歌引入了另一个名为 Bazel 的构建工具，他们已经使用了一段时间，现在这个工具作为开源工具发布。Bazel 是 angular 8 的一个可选选项，因此很明显，它还没有为 Angular 8 做好准备，预计将包含在第 9 版的 Angular CLI 中。使用该工具的预期好处，

更快的构建时间，尽管第一次构建通常需要时间，但并发构建需要的时间会更少。

我们现在能够以增量构建的方式构建应用程序，并部署 war 文件。

**TypeScript 3.4.x 支持**

Angular 8 支持 TypeScript 3.4 或更高版本，尽管如果自动更新不受限制，更新将被推送到应用程序。所以，如果你想用 Angular 8 进行应用开发，那么你需要先把 TypeScript 升级到 3.4 以上。谷歌已经在 600 多个内部项目上测试了 Angular 8，以确保向后兼容，这样已经运行的项目就不需要打补丁了。

阅读: [JavaScript vs TypeScript](https://www.decipherzone.com/blog-detail/JavaScript-vs-TypeScript)

**常春藤渲染引擎**

Ivy 是新的 Angular 编译器和工具，充当新的渲染管道。Ivy 的好处是它可以生成相当小的包，并且可以轻松地执行增量编译，但是它是如何做到这一点的呢？

*IVY 研究的两个主要概念*

1.  Tree shakeable:删除未使用的代码，以便应用程序可以专注于它正在使用的代码，为了有效地使用这一点，开发人员必须以避免条件语句的方式编写代码。如果引擎不清楚是否会使用该代码，则根本不会删除该代码。因此，过滤后的代码越小，运行时间就越快，包也就越小。

2.  Local:只重新编译正在改变的组件会导致更快的编译速度。

**Angular 8.0 搭配 IVY 的优势**

增强的有效负载尺寸

尺寸较小的构件

预编译代码的运输

高度向后兼容

快速重建时间

不需要元数据

在 Angular 8 中，谷歌只推出了 Ivy 的预览版。这个版本的主要目标是从 Angular 开发者社区获得早期反馈。

**性能优化的差分加载**

旧浏览器支持 ES5，现代浏览器支持 ES6。Angular CLI 8 生成传统(ES5)和现代(ES2015+) JavaScript 包作为构建过程的一部分，在客户端进行不同加载，以提高现代浏览器的加载速度和交互时间(TTI)。这项工作是与 Manfred Steyer 和他的项目 ngx-build-modern 合作完成的。

现在，开发人员可以指定他们的目标浏览器，CLI 将使用相关的必要 JS 包和必要的 polyfills 构建应用程序。tsconfig.json 文件中列出的默认目标浏览器现在是 es2015。这意味着当 CLI 8 构建应用程序时，它将为支持 ES6 功能的现代浏览器而构建。但是如果用户需要在 IE9 这样的旧浏览器中运行应用程序，那么 angular 开发者需要在浏览器列表文件中指定它。该文件存在于 CLI 项目的根文件夹中，以前仅用于 CSS 部分。现在，它也可以用于 JS 生成。

**路线**中惰性装载的变化

添加了简化大型项目升级路径的向后兼容模式。通过使用$ route APIs 允许部分 AngularJS 应用程序的延迟加载，这将使团队更容易迁移到 Angular 的最新版本。由于 ECMAScript 和 Ivy 的支持，在 Angular 8 中编写 lazy 模块的方式有所改变。

阅读:[Angular vs Vue:Web App 开发哪个好？](https://www.decipherzone.com/blog-detail/Angular-vs-Vue--Which-is-Better-For-Web-App-Development-)

**网络工作者支持**

Web workers 对于使用面向对象编程的核心概念来提高应用程序的速度至关重要。Web 工作人员在主线程之外编写代码。由于 JavaScript 总是以单线程方式执行，所以以异步方式执行大量数据调用或连续 Rest API 调用非常重要。因为在现实世界中，这个概念对我们没有帮助，这就是为什么今天所有的 web 浏览器都支持 web worker 进程。Web worker 进程是由浏览器在单独的线程中执行的脚本。浏览器选项卡中与 web worker 线程的通信是通过发送消息来完成的。因此，主要的议程是为每个 web 工作者提供一个单一的包，以便他们能够轻松地进行通信，Angular 8 中的 CLI 8 现在支持这个包。

**支持新的建筑商/建筑师 API**

Angular CLI 8 的新版本允许我们使用新版本的构建器，我们也可以用它来创建自定义的构建器。构建器(也称为 Architect API)是实现任务逻辑和行为的函数，它可以替换从@angular-devkit/architect 包传递给 createBuilder()方法的命令。Angular 使用 Builders API 来执行服务器、构建、测试、e2e 和 lint 等操作。

阅读:[什么是 API，它是如何工作的？](https://www.decipherzone.com/blog-detail/What-is-an-API-and-How-it-works-)

**选择加入使用共享以改善角度**

现在，在 Angular 8 中，Angular CLI 可以收集使用数据，以便 Angular 团队可以优先考虑功能和改进。因此，当我们更新 CLI 项目时，它将选择加入 ng analytics on 选项。Angular 8 希望通过收集一些数据(如使用的命令、使用的标志、操作系统、节点版本、CPU 数量、RAM 大小、执行时间和崩溃数据错误)来收集构建时间开发人员的偏好，以便在未来的版本中进行改进。

阅读:[web 应用开发的流程](https://www.decipherzone.com/blog-detail/The-process-of-web-application-development)

**升级角材**

如果您的应用程序使用了角形材料，您可以使用此命令更新组件:

$ ng 更新@角度/材料

**从角度 7 升级到角度 8**

正如前几个版本一样，将应用从 Angular 7 升级到 Angular 8 非常容易。如果您已经迁移到使用新的 HttpClient 和 RxJS 6，那就更好了。

您可以通过运行一个命令来升级 web 应用，以升级到 Angular 8:

$ ng 更新@angular/cli @angular/core

这样，即使您的延迟加载的路由导入也会自动迁移到新的导入语法。

***结论***

ViewChild、ContentChild、Differential loading 和 Ivy rendering Engine 的新紧凑语法显著降低了构建规模。同时 Bazel、web worker、TypeScript 3.4 及以上的支持使得 angular 代码运行速度惊人。如果通过利用更快的工具、组件和服务来提高代码可读性、减少应用程序的大小和提高速度都不能说服你使用 Angular 8，那么我认为即使是上帝也不能说服你做任何事情。

文章来源:[https://www . decipherzone . com/blog-detail/What-the-new-features-in-Angular-8](https://www.decipherzone.com/blog-detail/What-are-the-new-features-in-Angular-8)
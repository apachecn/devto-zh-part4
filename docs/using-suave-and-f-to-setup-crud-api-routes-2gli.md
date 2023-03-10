# 使用 Suave 和 F#设置 CRUD API 路径

> 原文：<https://dev.to/mokenyon/using-suave-and-f-to-setup-crud-api-routes-2gli>

F#是一种社区驱动的功能优先编程语言。它是一种多范式语言，可以很好地与 C#互操作，但提供了许多功能特性，使得使用它非常棒！

最近我花了一些时间学习 F#并通过 F#发现了 Suave:
Suave 是一个简单的 web 开发 F#库，它提供了一个轻量级 web 服务器和一组组合器来操纵路由流和任务组合。

如上所述，Suave 是一个可以用来创建 API 的 web 框架。当我研究 Suave 的时候，我没有找到一篇很好的文章来记录应该在 REST API 中使用的所有基本 CRUD 操作。

这是我试图把我从几个互联网资源中收集到的知识传递给大家。这不是一个端到端的应用程序，它只是记录了如何使用 Suave 来设置一个简单的 CRUD 应用程序所需的必要路线。

所有代码将位于一个 [github repo](https://github.com/MorganW09/SuaveAPI) 上。我的一个朋友把我介绍给了 Suave。他在他的[个人项目](https://gitlab.com/sNewell/trakr)中做的一些事情确实帮助我开始使用 Suave。他也在 F#兔子洞的更深处，所以如果你感兴趣的话，可以看看他的项目。

# F#和函数式编程语言

本文不是对 F#的介绍。如果你以前从未接触过这种语言，这里的一些例子可能会有点不同。如果是这样，欢迎来到函数式编程语言。我学习 F#的短暂时间让我大开眼界。

在我的职业生涯中，我用过 Java 和 C#。但是我错误地认为我学到的 Java 和 C#的最佳实践是整体编程的最佳实践。而实际上它们是面向对象语言的最佳实践。

函数式语言看待世界的方式非常不同。当你开始学习一门函数式语言时，一切都是新的，术语可能令人生畏。

有些人会被这个事实吓跑，但是我认为花时间去拥抱和学习一个新的范例是值得的。从长远来看，这只会让你受益。

如果你是函数式和 F#的新手，并且想要开始，这里有一个长达一小时的会议演讲，叫做[函数式设计模式](https://www.youtube.com/watch?v=srQt1NAHYC0)，作者是 Scott Wlaschin。斯科特还在 fsharpforfunandprofit.com[的](https://fsharpforfunandprofit.com/)写博客，这对你的起步来说是一个很好的资源。

在简短的功能性福音信息之后，让我们开始吧。

# 创建一个 API

打开你选择的 IDE，我用的是 Visual Studio，创建一个 F#控制台应用。使用您喜欢的任何名称，在创建之后，您应该会在 IDE 中看到类似下面的内容。

[![Alt Text](img/1c97bb5cd5066fc57fdc51a896dd9128.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ryzv2zot--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2j4ytx8zu5qjv4qvhm86.PNG)

接下来，我们将添加我们的依赖项。我们需要添加 Suave 和 Newtonsoft。Json 到我们的项目。右键单击“依赖项”= >“管理 nuget 包”，然后搜索并安装这两个 Nuget 包。

将 Program.fs 修改如下，以创建您的第一条路线。
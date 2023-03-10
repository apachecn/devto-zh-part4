# 揭开 ASP.NET 核心请求管道的神秘面纱:第 1 部分——接收请求

> 原文：<https://dev.to/jimbuck/demystifying-the-asp-net-core-request-pipeline-part-1-receiving-requests-i02>

> 这是 ASP.NET 核心请求管道 3 部分系列的第 1 部分。点击这里查看后续文章的链接！

1.  [前言](https://jimbuck.io/asp-net-core-req-pipe-0)
2.  第一部分-接收请求
3.  第二部分- *即将推出*
4.  第三部分- *即将推出*

[![Demystifying the ASP.NET Core Request Pipeline: Part 1 - Receiving Requests](img/dcce8f9d421624a2f5a55141e14001ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vcDfcXcb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1543674892-7d64d45df18b%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

对于许多开发人员来说，浏览器请求和控制器动作之间的区域是一片神秘和神奇的土地。有什么不应该的吗？在谈到路由和控制器时，MVC/WebAPI 和 ASP.NET 核心约定在 T2 是王道。只要您的命名方案遵循约定，您就可以放心地期待请求神奇地命中您的代码。

但是如果你不遵循惯例会发生什么呢？或者当您需要支持开箱即用的项目模板所不提供的更复杂的配置时？不要害怕，**请求管道不一定是黑盒**。对请求管道有一个坚实的理解可以让你在 sprint 结束时从**零到英雄**。

在本文中，我将分解每种类型的服务器实现，并概述它们的区别。查看最后的`tl;dr`表格，快速总结。之后，我概述了一些常见的场景，以及哪种实现最适合这项工作。

* * *

## 服务器实现

对于 web 应用程序来说，必须有某个端口(通常是 80 或 443)在监听以接收请求。监听和维护请求连接是 web 服务器的主要职责。说到 ASP.NET 核心，有几种实现和托管模式**各有各的好处**。

### 单机带红隼

[![Demystifying the ASP.NET Core Request Pipeline: Part 1 - Receiving Requests](img/5fe49c50d2e9eb40f5249406f671ea82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QpDs9bCw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimbuck.io/conteimg/2019/07/kestrel-standalone-1.png) 

<figcaption>独立式红隼</figcaption>

Kestrel 是专为 ASP.NET 核心打造的轻量级跨平台网络服务器。它是[开源](https://github.com/aspnet/AspNetCore/tree/master/src/Servers/Kestrel)，可以轻松[超过每秒 1M+的实际请求](https://msit.powerbi.com/view?r=eyJrIjoiYTZjMTk3YjEtMzQ3Yi00NTI5LTg5ZDItNmUyMGRlOTkwMGRlIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)，并且是 ASP.NET Core 3 启动新项目时的默认。Kestrel 将监听指定的主机/端口，并在您的 web 应用程序中运行。

一旦建立了连接，请求就由请求管道直接处理。对于新项目来说，这是一个很好的选择,因为没有额外的依赖，更好的是，它可以在 Windows、Mac 和 Linux 上工作！我强烈推荐在开发期间和开源项目中使用 Kestrel。

### 使用 IIS 和 Kestrel 的进程外

[![Demystifying the ASP.NET Core Request Pipeline: Part 1 - Receiving Requests](img/7b2a1ee86d1d54d6c8dfc053dd320325.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fAJioT_V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimbuck.io/conteimg/2019/07/iis-out-of-process-2.png) 

<figcaption>带红隼的进程外 IIS</figcaption>

当构建运行在 Windows 上的企业服务器应用程序时，您几乎总是会依赖事实上的 web 服务器: [IIS](https://www.iis.net/) 。最初是为 [Windows NT](https://support.microsoft.com/en-us/help/224609/how-to-obtain-versions-of-internet-information-server-iis#section-2) 和[发布的，为 Windows Server 2008](https://en.wikipedia.org/wiki/Internet_Information_Services#History) 重写，目前是第 10 版，为生产应用提供稳定性、安全性和管理工具。说 IIS 已经过实战考验是一种轻描淡写。任何企业开发人员/IT 专业人员都可能在职业生涯的某个阶段依赖于 IIS。

IIS 进程外托管使用 IIS 作为反向代理，将请求转发到运行在 Kestrel 上的 ASP.NET 核心应用程序(感谢[ASP.NET 核心模块](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/aspnet-core-module))。这种设置的主要好处是**能够托管和配置。NET 框架和。NET 核心应用**。这简化了 SSL、缓存和身份验证之类的配置，因为它们可以跨应用程序共享。

也可以使用其他反向代理( [Apache for Windows/Linux](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/linux-apache) ，以及 [Nginx for Linux](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/linux-nginx) )。虽然它们确实为其他平台提供支持，但 IIS 提供的专用支持将带来最佳体验。

### 带 IIS 的进程内

[![Demystifying the ASP.NET Core Request Pipeline: Part 1 - Receiving Requests](img/2c07108935c15a739b8dd4062241211e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q93LPWvs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimbuck.io/conteimg/2019/07/iis-in-process.png) 

<figcaption>进程内 IIS</figcaption>

从 ASP.NET 核心 2.2 开始，如果你的应用目标。净核心(不是完整的。NET Framework)，那么您就可以利用进程内宿主。这意味着 IIS 工作进程(`w3wp.exe`)实际上在同一个进程中加载 CoreCLR 和你的 ASP.NET 核心应用**。这使得**第一个字节**更快，因为它允许请求直接传递到管道中！**

这是提高性能的代价。每个 IIS 应用程序池只能使用一个应用程序，并且不支持。NET 框架。但是好处是每秒钟的请求数提高了 2-3 倍。

### HTTP.sys

T3【http . sys】T4

[HTTP.sys](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/servers/httpsys) 是一款 Windows 专用的网络服务器，专注于安全性和附加功能(与 Kestrel 相比)。**在 ASP.NET 核心 2.0 之前，Kestrel 不够安全，无法直接暴露于公共互联网**。HTTP.sys 允许在公共网站上访问应用程序，而不需要 IIS。此外，它还提供了一些 Kestrel 没有的功能，如 Windows 身份验证。

### 自定义 OWIN 服务器

[![Demystifying the ASP.NET Core Request Pipeline: Part 1 - Receiving Requests](img/1afbcf96ab17b3228484962271547965.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2IHQjQOW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jimbuck.io/conteimg/2019/07/custom-owin.png)T3】自定义 OWIN 服务器

如果 Kestrel/IIS 或 HTTP.sys 不支持某个功能，可以使用自定义服务器。符合的开放 Web 接口。这些服务器实现了一组用于管理 ASP.NET 核心应用并与之交互的接口。[你可以在这里了解`IServer`接口以及如何实现。](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/owin#using-aspnet-core-hosting-on-an-owin-based-server)

### TL；博士；医生

| 服务器实现 | 笔记 |
| --- | --- |
| 使用 IIS 的进程内 | 最高性能，但仅适用于 Windows。 |
| 红隼(独立) | 轻量级和跨平台。 |
| 带有 Kestrel 的进程外 IIS | 现有主机旁边。NET 框架应用程序。 |
| HTTP.sys | 高级功能，仅限 Windows。 |
| 自定义 OWIN 服务器 | 完全控制，最高的复杂性。 |

Summary of Server Implementations

## 哪个实现适合我？

以下场景只是几个需要考虑的可能情况。您的特定项目可能有特殊情况或来自您的组织的特定要求。

### 开源项目

茶隼(独立)可能是你最好的选择。开源项目的两个主要好处是**跨平台支持**和超级简单的工作流(**没有弄乱 IIS Express** )。虽然跨平台不是开源项目的要求，但没有什么比找到一个不支持您的平台的好项目更令人烦恼的了。现在，一个简单的开发人员工作流程是我们都应该努力实现的，但并不总是可以实现的。无论如何，使用独立的 Kestrel 无疑是新的开源项目的最佳选择。

### 带有遗留应用程序的企业项目

在大多数企业场景中，在构建新应用程序时，**必须考虑遗留应用程序。一些公司/管理者对网站如何托管有严格的 T2 规则。IIS 可以轻松地同时托管新的和旧的应用程序，但是您必须考虑两件事情。**

1.  如果性能(速度和内存使用)很重要，那么使用 IIS 的进程内是一个不错的选择。通过让 IIS 加载和执行 ASP.NET 核心应用程序，避免了反向代理 HTTP 调用，请求数据在内存中共享。结果是**更快的响应时间和更少的内存消耗**。
2.  如果共享应用程序池很重要，那么在 IIS 中使用进程外。IIS 将像一个反向代理，允许你在旧应用和新应用之间共享应用池。您的 ASP.NET 核心应用程序将运行一个 Kestrel 服务器，但将完全由 IIS 管理。虽然这确实降低了请求速度，但它确实在**共享安全性、可用性**等方面提供了好处。

### 绿地项目

对于全新的项目，Kestrel(独立)是您的最佳起点。Kestrel 提供了一个易于开发的轻量级服务器，允许您的团队花**更多时间专注于构建应用**。如果您正在为仅 Windows 开发并使用 IIS，则考虑使用 IIS 进程内部署。除了配置更简单之外，您还会发现内存使用更少，响应时间更快。

### 高绩效项目

如果你需要从你的技术堆栈中挤出每一盎司的性能，那么信不信由你，但 Kestrel(独立)可能是你最好的选择。就每秒请求数而言，ASP.NET 团队付出了巨大的努力使 Kestrel 成为顶级网络服务器。保持服务器性能的关键是通过使用**定制中间件，而不是大多数项目使用的“通用”**实现。当分解成简单的基于文本的请求时，**每秒可以处理大约 600 万个请求**。通过一些针对您的领域优化的智能认证和主体解析，您会惊讶于 Kestrel 的飞行速度。

### 不确定？

毫无疑问，第一个尝试的服务器是 Kestrel(单机版)。随着完整的安全实施(ASP.NET 核心 2.0 及更高版本)和高度敬业的团队将其性能推向最大化，**大多数项目将非常乐意依赖 Kestrel** 。最棒的是，您可以在开发过程中使用独立的 Kestrel，并通过 IIS 在进程内或进程外进行部署，只需进行一些配置更改。

## 结论

所有项目，*无论大小*，都在良好的开端中受益。提前做好计划，了解项目架构的可用选项。**最困难的项目并不总是有最复杂的逻辑；他们的高层规划最少。**

请关注本系列的第 2 部分，ASP.NET 核心请求管道。它将深入到管道配置和 MVC 中间件的细节！

有问题吗？评论？意见不合？在推特上给我打电话！
# ASP.NET core 3.0 preview 8-blazor 的新功能-新模板

> 原文：<https://dev.to/gustavobigardi/asp-net-core-3-0-preview-8-novidades-do-blazor-novos-templates-28ll>

嘿，伙计们，怎么样？

好吧，这是我在 DEV.TO 平台上的第一个职位之一，它吸引了我，因为开发人员和其他 it 专业人员都在这里发布内容。

上周(2019 年 8 月 13 日)，我们发布了新版本的. NET Core 3.0，即 Preview 8。与此同时，我们推出了 EF Core 和 ASP.NET Core 的新版本，因此也推出了 Blazor。

我会把新闻放在单独的文章里，这样就不会太大而无法阅读，每个人都可以把注意力集中在尚未更新或更感兴趣的地方，好吗？我们走！

# Novos Templates do Blazor

已更新并组织了模板，现在我们有两个模板，其中“模板”作为选项放置的过多。

*   **Blazor Server App** :这是以前的 Blazor Server-Side，在这里我们可以使用 Blazor 创建一个项目，在服务器上渲染视图，使用信号与浏览器进行通信。

*   **Blazor WebAssembly App** :这是以前的 Blazor 客户端，我们可以在其中创建 Blazor 项目，在客户端(浏览器)进行渲染，生成 WebAssembly 包，该包将直接在浏览器中运行我们的 ASP.NET 核心应用程序。此模板可以静态发布，甚至可以使用 ASP.NET core(kestreel)作为 Blazor 生成的静态资产的服务器。

要查看这些新模板以及已安装的. NET Core Preview 8，我们需要将 Visual Studio 2019 Preview 升级到 16.3 Preview 2 或更高版本。访问该菜单以创建新项目并按“Blazor”进行过滤时，我们有如下图所示的模板:

[![New Project - Blazor](img/149d2cb762a7073ea1e60a561222dcb0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zv1uy1EV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tvxt5v5zhj6a2igedy0f.png)

对于将 Visual Studio 代码与. net core CLI 配合使用的用户，可以使用以下命令安装或更新 Blazor 模板，同时牢记必须先将. net core 3.0 SDK 升级到 preview 8。

```
dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview8.19405.7 
```

使用 CLI 创建 Blazor App 和 Blazor WebAssembly App 项目的模板分别为“`blazorserver`”和“`blazorwasm`”。

要创建类型为“**”blazor web assembly app“**”的项目，并使用 ASP.NET Core 作为静态文件的主机，需要在项目创建屏幕上选中“**ASP.NET core hosted**”选项，或者要使用 CLI，请添加参数“”

```
dotnet new blazorwasm --hosted 
```

# blazor 组件的新模板

现在，新 Razor Class Lib 模板是用于创建 Razor Pages 或 Blazor 组件的默认模板，而旧的 Blazor Class Lib 模板已被删除。

另一个详细信息是，该模板基于标准. NET，允许与 Blazor 的 Server 版本和 WebAssembly App 一起使用。如果需要将整个视图和资产组件化，并将. NET 核心作为目标，则必须在项目创建屏幕上的“支持页和视图”选项中输入此信息，或使用参数“`--support-pages-and-views`”输入. NET 核心 CLI。

[![New Project - Razor Class Lib - Support Views and Pages](img/a865642335b0b2a89e4477d5a17376ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5XyxlzqE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y2okn23gx02da53oke2a.png)

新剃刀类图书馆通过。NET Core CLI:

```
dotnet new razorclasslib --support-pages-and-views 
```

# 太多模板

其他项目级模板也不再显示为“重复”，并且已进行了组织，仅显示在 Visual Studio 2019 Preview 或. net core CLI 的“创建新项目”菜单中。

*   工人服务
*   gRPC 服务

上面列出的第二个模板将在稍后的帖子中详细介绍，因为我们现在更好地支持 Visual Studio 和. net core CLI 来使用 grp 服务。

## 额外

角 SPA 应用程序模板现在已更新为使用角 8。因为系列的重点是布拉佐，所以我会在系列之外的另一个岗位上处理这件事，好吗？

# 打折训练

我们有现场训练的 50%折扣码实践中的蓝色。

[![Logo - Azure na Prática](img/31f566fa31a05b2b04fdb79873d15fb0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iVcGrkY3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/odmkmuxflc3ewtfvul34.png)

实际上，蓝色来自演讲者和来自丰塞卡的微软 mvps ericson、Milton Camara Gomes 和 Renato Groffe 在过去几年参加的各种活动中所看到的需求。

在这些技术会议上，他们发现了一些反复出现的问题，即公司迁移到云的最佳方式、使用哪些服务，以及如何在不增加过度成本的情况下完成这一转变过程，并最大限度地提高 it 员工的工作效率。

面对这一切，他们(Ericson、Milton 和 Renato)的想法应运而生，他们通过一门与现在市场上完全不同的课程来分享他们的经验。一种完全实用的培训，重点介绍蓝筹学的主要特点，使学习更加轻松、动态。

请记住，培训将非常实用，也就是说，每位学员都必须携带笔记本电脑运行平台。

要注册 50%折扣，只需使用代码“`jundevelopers`”或访问以下链接即可，该链接已引导您前往已应用折扣代码的研讨会。

[https://www.sympla.com.br/azure-na-pratica__605145?d=jundevelopers](https://www.sympla.com.br/azure-na-pratica__605145?d=jundevelopers)

# 总结

这只是. net core 3.0 preview 8 中 Blazor 的一小部分新功能。我正在分解部分，以便更详细地介绍每一个新闻，而不是让文章过于冗长，更频繁地发表。跟进系列新闻，本周我们还会有新帖子。

# 参考文献

此帖子参考/翻译了有关. NET Core 3.0 Preview 8 发行版的原始帖子，可在以下网址找到:[https://devblogs . Microsoft . com/aspnet/ASP-net-core 和 blazor 更新-in-net-core-3-0-preview-8/](https://devblogs.microsoft.com/aspnet/asp-net-core-and-blazor-updates-in-net-core-3-0-preview-8/)
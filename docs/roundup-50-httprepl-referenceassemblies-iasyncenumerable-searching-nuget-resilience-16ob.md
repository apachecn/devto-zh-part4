# 综述# 50:http prepl，ReferenceAssemblies，IAsyncEnumerable，Searching Nuget，Resilience

> 原文：<https://dev.to/codeopinion/roundup-50-httprepl-referenceassemblies-iasyncenumerable-searching-nuget-resilience-16ob>

[![](img/4c50ef74f19fbed0b4a253ee27c75123.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N0OFoElO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeopinion.com/wp-content/uploads/2019/01/Roundup.png)

以下是最近在. NET 中引起我注意的事情。我很想听听你本周发现的最有趣的事情。请在评论或推特上告诉我。

[关注@codeopinion](https://twitter.com/codeopinion?ref_src=twsrc%5Etfw)

## [HTTP prepl:用于与 RESTful HTTP 服务交互的命令行工具](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/)

> ASP.NET 团队已经开发了一个名为[http prepl](https://github.com/aspnet/HttpRepl)的命令行工具。它允许您以类似于处理文件和文件夹的方式浏览和调用 HTTP 服务。您给它一个起点(一个基本 URL)，然后您可以执行像“dir”和“cd”这样的命令来浏览 API。

链接:[https://dev blogs . Microsoft . com/aspnet/http prepl-a-command-line-tool-for-interactive-with-restful-http-services/](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/)

## [使用 ReferenceAssemblies NuGet 包来构建。NET 框架库，无需安装 Mono](https://andrewlock.net/using-reference-assemblies-to-build-net-framework-libararies-on-linux-without-mono/)

> 在这篇文章中，我将展示如何构建。NET 项目。NET 框架版本在 Linux 上，*不用 _ 使用 Mono。通过使用[新的](https://www.nuget.org/packages/Microsoft.NETFramework.ReferenceAssemblies/1.0.0-preview.2)* [微软。net framework . reference assemblies](https://www.nuget.org/packages/Microsoft.NETFramework.ReferenceAssemblies/1.0.0-preview.2)_[n 从微软获取软件包](https://www.nuget.org/packages/Microsoft.NETFramework.ReferenceAssemblies/1.0.0-preview.2)除了。网芯 SDK！

链接:[https://Andrew lock . net/using-reference-assemblies-to-build-net-framework-libararies-on-Linux-without-mono/](https://andrewlock.net/using-reference-assemblies-to-build-net-framework-libararies-on-linux-without-mono/)

## [IAsyncEnumerable<T>in 有什么大不了的。网芯 3.0](https://dev.to/dotnet/what-s-the-big-deal-with-iasyncenumerable-t-in-net-core-3-1eii)

> 最激动人心的特性之一。NET Core 3.0 和 C# 8.0 已经增加了`IAsyncEnumerable<T>`(又名异步流)。但是它有什么特别之处呢？我们现在能做以前不可能的事情吗？
> 
> 在本文中，我们将看看`IAsyncEnumerable<T>`打算解决什么挑战，如何在我们自己的应用中实现它，以及为什么`IAsyncEnumerable<T>`将在许多情况下取代`Task<IEnumerable<T>>`。

链接:[https://dev . to/dot net/what-s-the-big-deal-with-iasyncenumerable-t-in-net-core-3-1 EII](https://dev.to/dotnet/what-s-the-big-deal-with-iasyncenumerable-t-in-net-core-3-1eii)

## [用 Azure 函数索引和搜索 NuGet.org 和搜索](https://blog.maartenballiauw.be/post/2019/07/30/indexing-searching-nuget-with-azure-functions-and-search.html)

> 在我编写的应用程序中，我需要反序列化一些 JSON。我知道要使用的类是`JsonConvert`，但是那个类是在哪个 NuGet 包中输入的呢？
> 
> 当然，这是显而易见的。然而，**“NuGet 反向包搜索”**有许多用途，它有助于根据公共类型找到正确的 NuGet 包。
> 
> 虽然 ReSharper 和 Rider 最初添加了这个不错的功能，Visual Studio 也在几年后添加了这个功能，但我想看看是否可以**构建一个索引器和搜索引擎**，从 NuGet 包中收集公共类型信息，并使它们可以被搜索。在这个过程中，我发现这将是 **[Azure 函数](https://azure.microsoft.com/en-in/services/functions/)** 的理想用例。

链接:[https://blog . maartenballiauw . be/post/2019/07/30/indexing-search-nuget-with-azure-functions-and-search . html](https://blog.maartenballiauw.be/post/2019/07/30/indexing-searching-nuget-with-azure-functions-and-search.html)

## [系统稳定:健壮的应用程序与 Polly 的连接。网络弹性框架——布莱恩·霍根](https://www.youtube.com/watch?v=esEDPtBJz40)

> 在本节中，我将向您展示如何通过几行代码让您的应用程序更具弹性和可靠性。与波莉。NET 弹性框架，您的应用程序可以很容易地容忍远程系统或基础设施中的瞬时故障和较长时间的中断。
> 
> 在这一小时结束时，您将知道如何使用 Polly 的所有功能来使您的应用程序成为坚如磐石的作品。我们将讨论被动和主动弹性策略，从简单但非常强大的重试开始，到隔板隔离结束。
> 
> 哦，我有没有提到 Polly 是一个. NET 标准库，所以它可以在你能想到的任何应用程序上工作！
> 
> 加入我一个小时，你的应用程序将不再一样。

链接:[https://www.youtube.com/watch?v=esEDPtBJz40](https://www.youtube.com/watch?v=esEDPtBJz40)

[关注@codeopinion](https://twitter.com/codeopinion?ref_src=twsrc%5Etfw)

帖子[round up # 50:http prepl，ReferenceAssemblies，IAsyncEnumerable，Searching Nuget，Resilience](https://codeopinion.com/roundup-50/) 最先出现在 [CodeOpinion](https://codeopinion.com) 上。
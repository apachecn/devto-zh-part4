# 有棱角的和其他的

> 原文：<https://dev.to/thisdotmedia/angular-and-the-rest-4ho7>

本周和下周，我将撰写一系列新文章，介绍如何在客户端使用 Angular v8 开发一个全栈应用程序，分别使用 ASP.NET Core 2.2 和 Node.js。

本系列的前两部分将涵盖构建 Angular 应用程序并将其连接到 ASP.NET 核心 Web API 应用程序。后端 Web API 使用 SQLite 数据库进行存储，并通过 JWT (JSON Web 令牌)对用户进行身份验证。

后两篇文章将讨论如何使用同一个 Angular 应用程序，不过这一次，将它连接到一个后端 Node.js API 应用程序(很可能使用 Nest.js)。后端 API 使用 PostgreSQL 数据库进行存储，并通过 JWT 验证用户身份。

Angular 由 Google 创建和维护，主要用于开发单页面应用程序(SPA)。另一方面，ASP.NET 核心由微软创建和维护，可用于设计和构建 RESTful Web API，为客户端应用提供服务，包括但不限于 Angular 应用。

今天，我将开始构建 Angular 应用程序来跟踪我已经观看过的电影和未来将要观看的电影。Angular 应用程序允许我在电影上执行所有 CRUD(创建读取更新和删除)操作。从后端来看，我将构建一个 ASP.NET 核心 Web API。目前，它将提供一个 RESTful 端点来执行电影上的所有 CRUD 操作，并连接到 SQLite 数据库。此外，我将向您展示如何使用 NSwag 来使 Web API 生成 Swagger API 文档。在 NSwagStudio 的帮助下，我们可以基于 Web API 端点生成 TypeScript 服务，并嵌入 Angular app 内部。这样，就不需要生成这段代码，节省了时间。

> 简而言之，Swagger 为 Web API 生成文档，允许任何消费者了解这个 API 的功能以及如何调用不同的端点。你可以在 [Swagger 网站](https://swagger.io/)上了解更多信息。
> 
> NSwag Nuget 包为您提供了配置 Web API 以生成 Swagger API 文档所需的 API。你可以在 [NSwag Github](https://github.com/RicoSuter/NSwag) 上了解更多信息。
> 
> NSwagStudio 是由 NSwag 团队提供的桌面应用程序，允许您进行代码生成。他们也提供了命令行。你可以在这里阅读更多关于它的内容。

让我们开始构建后端 Web API。

## 构建 ASP.NET 核心 Web API

要开始使用 ASP.NET 核心构建 Web APIs，请确保您的机器上本地安装了以下工具和框架。

*   。NET Core 2.2 框架[下载](https://dotnet.microsoft.com/download/dotnet-core/2.2)
*   Visual Studio 2019 [下载](https://visualstudio.microsoft.com/vs/)

我将在 Windows 机器上开发这个应用程序。你可以根据自己的喜好随意使用 Macbook 或任何其他机器。

在本节的剩余部分，我们将一步一步地介绍如何开发 Web API。

后端 Web API 的源代码可以在这个 Github [repo](https://github.com/bhaidar/movietracker-server) 上找到。

**步骤 1**
打开 Visual Studio 2019，定位并点击**新建项目**按钮，如下图所示。

[![Visual Studio 2019 - Create Project](img/16941f65e0dcf8dda32209f8d60902cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aJTe_B43--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o9mk9iwetulwipjkr873.png)

**步骤 2**
搜索**ASP.NET 核心 Web 应用**模板，然后点击**下一步**按钮。

[![ASP.NET Core Project Template](img/b419a21f416ba13b8d88d3bc1e5bb7ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aYgq2uZP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rjheakod2qkwfo2qafy3.png)

**第三步**
提供一个**项目名称**，**位置**并点击**创建**按钮。

[![Configure Project](img/129a73b69ccb70e96107e452bcc4c87d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NHLMpFVP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/64avyyb8610xrn0fi2lx.png)

**步骤 4**
确保选择了**空的**应用类型，并点击**创建**。

[![Empty Project](img/b33865354d296e3f51e1210bfae918b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aDBxwpr0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uyuzx5ytet5n1qnvu9af.png)

这将创建一个新的空 ASP.NET 核心应用程序:

[![Visual Studio Project Created](img/23eb96fa9b95a3326f6ce0be5c979782.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3yJ27Vyj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4jhw88zmpbg0ecj0m5fl.png)

> 这篇文章的范围仅限于使用 ASP.NET 核心，没有深入探讨。如果你想了解更多关于 ASP.NET 核心的信息，你可以访问官方的[ASP.NET 核心文档](https://docs.microsoft.com/en-us/aspnet/core/?view=aspnetcore-2.2)。

**第五步**
在应用程序根目录下的 **Model** 文件夹中添加一个名为 **Movie.cs** 的新类，如下所示:
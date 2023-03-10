# 棱角与其余——与 JWT 的认证

> 原文：<https://dev.to/thisdotmedia/angular-and-the-rest-authentication-with-jwt-5em0>

在 Angular 系列的第二部分以及其他文章中，我使用 JWT (JSON Web Token)在后端 ASP.NET 核心 Web API 上实现了身份验证。此外，我在 Angular 应用程序端添加了一个新的认证模块，因此只有通过认证的用户才能登录。

让我们回顾一下到目前为止我们在这个系列中所取得的成就。我们构建了后端 ASP.NET 核心 Web API 应用程序，它定义了一个电影跟踪器端点，并完整实现了 CRUD 操作和电影搜索。在客户端，我们构建了一个 Angular v8 应用程序，它通过使用 Swagger API 文档、NSwag 和 NSwagStudio 生成的电影跟踪器服务与后端 RESTful Web API 进行通信。该应用程序显示所有电影，允许用户过滤电影，添加新电影，编辑现有电影，并可以删除电影。

这里是这个系列的第一篇文章 的链接。

今天，让我们首先将 JWT 认证添加到我们的 ASP.NET 核心 Web API 中。

## 向 ASP.NET 核心 Web API 添加 JWT 认证

要开始使用 ASP.NET 核心构建 Web APIs，请确保您的机器上本地安装了以下工具和框架。

*   。NET Core 2.2 框架[下载](https://dotnet.microsoft.com/download/dotnet-core/2.2)
*   Visual Studio 2019 [下载](https://visualstudio.microsoft.com/vs/)

我们，出于所有的意图和目的，将在 Windows 机器上开发这个应用程序。你可以随意使用 Macbook 或任何你喜欢的机器。

在本节的剩余部分，我们将逐步介绍如何将 JWT 认证添加到 ASP.NET 核心 Web API 应用程序中。

后端 Web API 的源代码可以在这个 Github [repo](https://github.com/bhaidar/movietracker-server/tree/jwt-authentication) 上找到。

**步骤 1**
要开始在我们的应用程序中验证用户，我们需要首先开始管理用户。因此，让我们修改 **MovieTrackerContext** 来迎合我们应用中的用户。

将上下文类的头修改成这样:

```
public class MovieTrackerContext : IdentityDbContext<ApplicationUser> 
```

Enter fullscreen mode Exit fullscreen mode

通过从 **IdentityDbContext** 类继承，我们保证实体框架(EF)核心将在数据库中创建所有必要的用户相关表。

IdentityDbContext 需要在应用程序中定义一个用户类，以便在数据库中创建用户表。默认情况下，IdentityDbContext 类使用 IdentityUser 来定义许多标准用户字段，如下所示:
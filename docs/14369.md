# 将 EasyAuth(应用服务认证)与 ASP.NET 核心一起使用

> 原文：<https://dev.to/azure/using-easyauth-appservice-authentication-with-asp-net-core-1cj1>

Azure 应用服务中有一个很酷的功能，我很喜欢。它被称为 EasyAuth，虽然它可能不再使用这个名字。

当您正在创建一个项目，并且想要加入一些快速的身份验证时，单点登录(简称 SSO)是一个很好的方式，在您继续交付价值的同时将身份验证问题抛给其他人。

当然，你可以清楚地了解[是如何工作的](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization?WT.mc_id=devto-blog-marouill#how-it-works)，但我想我可以总结得相当快。

EasyAuth 的工作方式是拦截认证请求(`/.auth/*`)，或者在通过认证后，填充应用程序中的用户上下文。那是 5 秒钟的音高。

现在，这个[。NET 框架应用程序生命周期](https://support.microsoft.com/help/307985/info-asp-net-http-modules-and-http-handlers-overview?WT.mc_id=devto-blog-marouill)允许在你的应用程序中添加一个`HttpModule`时发生大量的事情。你可以接触任何东西和厨房的水槽。

。另一方面，NET Core 去掉了全能模块的概念，转而引入了中间件。我们可以根据应用程序的需要扩展管道，而不是依赖管道中发生的一组固定事件。

我不打算详细说明如何移植 HttpModules 和 Handlers，但是让我们假设它们有很大的不同。

其中一个不同之处是`HttpModules`可以在`web.config`文件中设置，而配置文件可以在机器级别定义。这对于中间件来说是不可能的。至少，现在还没有。

## 为什么有关系？

那么，所有这些变化，为什么对 EasyAuth 很重要呢？应用程序编程模型发生了很大的变化。NET Framework 停止使用。网芯。

我确信微软会有解决方案，但是我遇到的一个客户遇到了这个问题，我想解决这个问题。

## 解决问题

因此，在[了解了 EasyAuth 如何工作](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization?WT.mc_id=devto-blog-marouill#how-it-works)之后，我开始着手创建一个[库](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)以及一个 [NuGet 包](https://www.nuget.org/packages/MaximeRouiller.Azure.AppService.EasyAuth/)。

我要做的是将捕获的身份和声明传递到。NET 核心身份验证管道。我没做别的事。

## 安装解决方案

第一步是使用您选择的方法安装 [NuGet 包](https://www.nuget.org/packages/MaximeRouiller.Azure.AppService.EasyAuth/)。然后，给你的控制器添加一个`[Authorize(AuthenticationSchemes = "EasyAuth")]`。

最后，将以下代码行添加到您的`Startup.cs`文件中。

```
using MaximeRouiller.Azure.AppService.EasyAuth
// ...
public void ConfigureServices(IServiceCollection services)
{
    //... rest of the file
    services.AddAuthentication().AddEasyAuthAuthentication((o) => { });
} 
```

就是这样。如果您的控制器有一个`[Authorize]`属性，凭证将自动开始填充您的 MVC 控制器的`User.Identity`。

## 问题

我应该走得更远吗？您希望将它集成到受支持的 Microsoft 包中吗？直接在 Twitter 或 T2 上联系我，还有很多其他的方式。
# ASP.NET 核心路由教程

> 原文：<https://dev.to/thomasardal/asp-net-core-routing-tutorial-40al>

在本帖中，我们将深入探讨 ASP.NET 核心中的新路由功能。如果你有 ASP.NET MVC 和 Web API 的背景，路由对你来说应该不是一个新概念。对于那些刚开始在。NET 中，路由负责将请求 URL 映射到控制器形式的处理程序。当收到请求时，Core 会寻找一个能够根据一组规则处理传入请求的处理程序。事实上，路由“仅仅”是一个中间件，我们在[之前的文章](https://blog.elmah.io/error-logging-middleware-in-aspnetcore/)中已经了解过。

要创建新的路由，您需要了解*路由模板语法*。虽然路由语法从一开始就在 ASP.NET MVC 中可用，但 Core 极大地扩展了这种可能性。在 Core 中创建新的 web 应用程序时，会在`Startup.cs`中自动设置默认路由。如果您从一个空项目开始，您将需要安装[微软。AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/) NuGet 包:

```
Install-Package  Microsoft.AspNetCore.Routing 
```

要将 MVC 添加到 web 项目中，调用`Startup.cs`中的`UseMvc`-方法，并给它一个默认模板，如下所示:

```
app.UseMvc(routes =>
{
    routes.MapRoute(
        name: "default",
        template: "{controller=Home}/{action=Index}/{id?}");
}); 
```

(如果您通过 Visual Studio 创建一个新的 MVC 应用程序，NuGet 包会自动安装，并且将`UseMvc`-方法添加到`Startup.cs`)

在`UseMvc`-方法中指定的模板将告诉内核将所有请求匹配到`/some/url/42`，匹配到一个名为`SomeController`的控制器，一个名为`Url`的动作接受一个`id`参数。注意到模板里面的花括号了吗？花括号内的内容代表一个片段，我们稍后将回到这个片段。与以前不同，默认值可以作为段的一部分嵌入，而不是通过`defaults`参数(尽管仍然支持)。在上面的例子中，请求`/`将触发`HomeController`的`Index`动作，而没有任何`id`参数或者该参数的值被设置为`null`。可选参数后面带有一个问号(`id?`)。

除了内联默认值和可选参数，我们还没有看到任何在以前版本的 ASP.NET 中没有的东西。让我们看看核心路由中添加的一些新特性。

路线约束，为更好地控制路线中允许的值打开了大门。我们大多数人都和 ASP.NET MVC 一起工作过，花了几个小时试图找出为什么一条路线和一个控制器不匹配。路线约束是一个新特性，它会把一些约束(D'oh！)在一个或多个段中的可能值上。我们来看一个例子:

```
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}"); 
```

在这个例子中，我们告诉 Core，`id`参数的类型必须是`integer`。这告诉 Core 不要映射下面的请求，因为 URL 的最后一段是一个字符串:`some/url/somestring`。您甚至可以使用路由约束定义整数范围、字符串最大长度等等。关于可能性的更多信息，查看官方[路线约束参考](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/routing#route-constraint-reference)。

另一个新特性是通配符的使用。通配符充当一种总括，可以用作任何段的一部分。示例:

```
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}/{*more}"); 
```

这将把请求映射到`/some/url/42`和`/some/url/42/details`。它甚至会映射`/some/url/42/details/show`，因为`*`充当零个或更多分段的通配符。

像前面一样，路由模板可以在属性中指定，而不是在启动时指定:

```
[Route("home/index/{id:int}/{*more}")]
public IActionResult Index(int id)
{
    ...
    return View();
} 
```

## 你的用户会喜欢更少的错误吗？

elmah.io 是一个简单的错误记录和正常运行时间监控服务。通过对所有的支持，重新控制你的错误。NET web 和日志框架。

➡️ [错误监控。NET 网络应用](https://elmah.io/?utm_source=devto&utm_medium=social&utm_campaign=devtoposts) ⬅️

这篇文章最初出现在 elmah.io 的博客上，时间是[https://blog.elmah.io/aspnetcore-routing-tutorial/](https://blog.elmah.io/aspnetcore-routing-tutorial/)
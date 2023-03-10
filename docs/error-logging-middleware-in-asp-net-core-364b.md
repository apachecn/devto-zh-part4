# ASP.NET 核心中的错误记录中间件

> 原文：<https://dev.to/thomasardal/error-logging-middleware-in-asp-net-core-364b>

这篇文章是关于 ASP.NET 核心中间件的概念。这篇文章被命名为 ASP.NET 核心中的*错误日志中间件，因为我想用错误日志作为一个利用中间件的例子。本文示例中展示的中间件概念并不局限于错误日志，而是可以作为构建所有类型中间件的基础。*

中间件是作为核心中请求管道的一部分执行的代码组件。如果你有 ASP.NET 的背景，并认为这听起来很熟悉，你就对了。正如你从 ASP.NET 那里了解到的，中间件基本上是 HTTP 模块。模块和中间件最大的区别在于你如何配置它。模块是在`web.config`中配置的，因为 Core 不使用`web.config`的概念，所以你在 C#中配置中间件。如果你了解 Express for Node.js，你会发现配置中间件大量借用了其中的许多概念。

让我们等着听更多关于中间件的废话，然后看一个例子。中间件最简单的形式是 C#类。让我们创建一些错误日志中间件:

```
public class ErrorLoggingMiddleware
{
    private readonly RequestDelegate _next;

    public ErrorLoggingMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task Invoke(HttpContext context)
    {
        try
        {
            await _next(context);
        }
        catch (Exception e)
        {
            System.Diagnostics.Debug.WriteLine($"The following error happened: {e.Message}");
            throw;
        }
    }
} 
```

为了让我们的中间件工作，我们需要实现两件事。接受一个`RequestDelegate`和一个`Invoke`方法的构造函数。构造函数只被调用一次，但是基础委托会随着请求的不同而变化。所有中间件组件负责执行流水线中的下一个链接(`_next`)或者通过不调用`_next`来终止流水线。在我们的例子中，我们希望执行管道的其余部分，以便捕捉在处理 HTTP 请求时发生的任何异常。当一个异常被捕获时，我们记录一条消息给`System.Diagnostics.Debug`。在现实生活中，您可能希望在某个更好的地方进行日志记录，但是对于演示来说，我们希望该异常只出现在 Visual Studio 中。

注意，`catch`块在将异常记录到`System.Diagnostics`后抛出异常。抛出异常可以确保处理异常的其他中间件仍然工作。

为了告诉 Core 我们新的闪亮的中间件，在`Startup.cs` :
中配置它

```
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory fac)
{
    ...
    app.UseMiddleware<ErrorLoggingMiddleware>();
    ...
} 
```

确保在安装其他处理异常的中间件(如`UseExceptionHandler`)后调用`UseMiddleware`-方法。一些中间件类“吞下”异常，这导致您的 catch 块没有被触发。通过将您的中间件添加为最后一部分，该类将在靠近失败代码的地方被调用。

为了测试中间件，让`HomeController`中的`Index`-方法抛出一个异常:

```
public class HomeController : Controller
{
    public IActionResult Index()
    {
        throw new Exception("Some error yo");
    }
} 
```

启动项目时，该异常现在被记录到 Visual Studio 的输出窗口中:

[![Debug message in output](img/3c574bc1598d7e91ddfc7de51096d453.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9LhB54G0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.elmah.io/conteimg/old/debug_message_in_output.png)

成功！我们刚刚为 Core 实现了第一个功能中间件。

当调用`Startup.cs`中的`UseMiddleware<>`方法时，创建一个自定义的`Use`方法:
被认为是一个好的实践

```
public static class ErrorLoggingMiddlewareExtensions
{
    public static IApplicationBuilder UseErrorLogging(this IApplicationBuilder builder)
    {
        return builder.UseMiddleware<ErrorLoggingMiddleware>();
    }
} 
```

调用`Startup.cs` :
中的静态方法

```
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory fac)
{
    ...
    app.UseErrorLogging();
    ...
} 
```

关于如何为核心实现全功能错误日志中间件的示例，请查看我们在 [GitHub](https://github.com/elmahio/Elmah.Io.AspNetCore/blob/master/Elmah.Io.AspNetCore/ElmahIoMiddleware.cs) 上为 ASP.NET 核心提供的 [elmah.io 支持。](https://docs.elmah.io/logging-to-elmah-io-from-aspnet-core/)

## 你的用户会喜欢更少的错误吗？

elmah.io 是一个简单的错误记录和正常运行时间监控服务。通过对所有的支持，重新控制你的错误。NET web 和日志框架。

➡️ [错误监控。NET 网络应用](https://elmah.io/?utm_source=devto&utm_medium=social&utm_campaign=devtoposts) ⬅️

本文首次出现在 elmah.io 博客上，网址为[https://blog . elmah . io/error-logging-middleware-in-aspnetcore/](https://blog.elmah.io/error-logging-middleware-in-aspnetcore/)
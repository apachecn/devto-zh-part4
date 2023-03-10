# 使用错误过滤挂钩丰富 ELMAH 错误

> 原文：<https://dev.to/thomasardal/enrich-elmah-errors-using-error-filtering-hook-38k7>

我看到很多人询问 ELMAH·胡克斯，希望他能在给 ELMAH 写信之前修改错误。虽然 elmah.io 客户端支持修改消息，但开源形式的 elmah 不支持。

也就是说，还是有办法实现这一点的。假设您想要覆盖所有错误的用户字段，使用从数据库中获取的值。默认情况下，ELMAH 用值`Thread.CurrentPrincipal.Identity.Name`注释所有错误，但这可能不是您想要在所有情况下记录的。

为了挂钩到 ELMAH，我们将使用一个真正用于其他目的的挂钩:错误过滤。给`Global.asax.cs`添加一个新方法，命名为`ErrorLog_Filtering` :

```
void ErrorLog_Filtering(object sender, ExceptionFilterEventArgs args)
{
} 
```

ELMAH 在记录所有异常之前调用此方法。我们不能访问这个方法中记录的实际错误对象，这就是为什么我们不能设置一个新的用户名。但是使用一个小技巧，我们仍然可以实现我们想要做的事情

```
void ErrorLog_Filtering(object sender, ExceptionFilterEventArgs args)
{
    var httpContext = args.Context as HttpContext;
    if (httpContext != null)
    {
        var error = new Error(args.Exception, httpContext);
        error.User = GetUserFromDatabase();
        ErrorLog.GetDefault(httpContext).Log(error);
        args.Dismiss();
    }
} 
```

看到我做了什么吗？我简单地创建了一个新的`Error`对象，并将`User`属性设置为`GetUserFromDatabase`的值。该方法是为示例创建的，可以是内存中的任何变量，也可以是 REST 请求的结果。

当新的错误被创建时，我调用 ELMAH 上的`Log`方法将它记录到已配置的存储中。为了避免记录两个错误(没有用户的原始错误和包括用户的新错误)，对原始错误参数调用`Dismiss`-方法很重要。

就是这样！现在，所有错误都用自定义值记录在`User`中。同样的方法可以用于设置 ELMAH 的`Error`对象上的任何变量。

## 你的用户会喜欢更少的错误吗？

elmah.io 是一个简单的错误记录和正常运行时间监控服务。NET web 和日志框架。

➡️ [错误监控。NET 网络应用](https://elmah.io/?utm_source=devto&utm_medium=social&utm_campaign=devtoposts) ⬅️

本文最早出现在 elmah.io 博客上的[https://blog . elmah . io/enrich-elmah-errors-using-error-filtering-hook/](https://blog.elmah.io/enrich-elmah-errors-using-error-filtering-hook/)
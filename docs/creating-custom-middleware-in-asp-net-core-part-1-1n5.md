# 在 ASP.NET 核心中创建定制中间件(第 1 部分)

> 原文：<https://dev.to/cooperaustinj/creating-custom-middleware-in-asp-net-core-part-1-1n5>

原帖:[https://Austin Cooper . dev/2019/08/25/creating-custom-middleware-in-。net-core/](https://austincooper.dev/2019/08/25/creating-custom-middleware-in-.net-core/)

## 什么是中间件？

简单地说，中间件是成为处理 web 应用程序中的请求和响应的管道的一部分的代码。在 ASP.NET 核心，中间件是一件大事！所有请求都由中间件管道处理。

[![Middleware Flow](img/6d999ceb72473ae0c4e2a62bc13c4043.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kwDfysR1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oygysmf4brke1eycd8uk.png)

如果你以前创建过 ASP.NET 核心应用，你可能知道`UseDeveloperExceptionPage`中间件。这个中间件增加了特殊的异常处理。如果稍后在管道中抛出异常，异常会出现在这个中间件上，它会返回一个包含详细信息的页面作为对请求的响应。因为中间件是一个管道，所以顺序很重要。对于开发人员异常页面，重要的是它是添加到管道中的第一批中间件之一。

在 GitHub 上找到这篇文章的代码！

## 入门

对于本指南，我从使用`dotnet new web`创建一个项目开始。这个命令用最少的样板文件创建了一个 ASP.NET 核心项目。

```
// Startup.cs
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
} 
```

即使在这个最基本的项目中，我们已经有了两个中间件组件:`UseDeveloperExceptionPage`和`Run`。我们已经知道了`UseDeveloperExceptionPage`。`Run`是一个*终端中间件*。终端中间件停止处理请求的附加中间件。

是我们可以使用的另一个中间件。不一定*像`Run`一样是终端中间件。它可以是终端的，也可以是非终端的，这取决于下一个中间件是否被调用。`Use`将是我们如何编写我们的第一个定制中间件。*

 *## 自定义中间件

让我们给我们的`Startup.Configure`方法添加一些定制的中间件。

```
- public void Configure(IApplicationBuilder app, IHostingEnvironment env) + public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILogger<Startup> logger)
 {
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
+
+    app.Use(async (context, next) =>
+    {
+        logger.LogInformation("Request started");
+        await next.Invoke();
+        logger.LogInformation("Request finished");
+    }); 
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
 } 
```

我们添加了一个日志记录器和一个`app.Use`部分。这总是一个非终端中间件，因为我们为中间件中的所有代码路径调用`next.Invoke`。要尝试一下，运行`dotnet run`并导航到`http://localhost:5000`。你应该在页面上看到`Hello World!`，在控制台上看到类似这样的内容:

```
info: CreatingMiddleware.Startup[0]
      Request started
info: CreatingMiddleware.Startup[0]
      Request finished 
```

## 增强！

因此，我们创建了一个超级简单的定制中间件，它记录请求开始和完成的时间。凉爽的...但是我们还能做什么呢？让我们对我们的`app.Use`块做一些修改...

```
app.Use(async (context, next) =>
{
    if (context.Request.Path.ToString().StartsWith("/test"))
    {
        await context.Response.WriteAsync("Welcome to Test");
    }
    else
    {
        await next.Invoke();
    }
}); 
```

现在，当请求路径以`/test`开始时，我们的中间件是终端的，返回响应`Welcome to Test`。要进行验证，请导航至`http://localhost:5000/test`。对于不是以`/test`开头的路由，我们只是直通到下一个中间件。

您可能已经注意到，我们可以访问中间件中的整个`HttpContext`。我们可以检查用户是否经过身份验证，从路由中提取信息，并读取请求的主体。当然，这些只是我们可以从`HttpContext`获得的信息的一些例子。

## 现在就这些

中间件是一个相当深的兔子洞，所以我打算把这篇文章放在这里。我还有更多的东西要写，但那将不得不在以后的帖子中出现——我不想在帖子中写得太多。请访问我的个人网站 http://austincooper.dev 进行订阅，并在第 2 部分发布时收到通知。*
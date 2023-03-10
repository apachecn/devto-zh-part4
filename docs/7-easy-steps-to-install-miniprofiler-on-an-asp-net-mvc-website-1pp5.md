# 在 ASP.NET MVC 网站上安装 MiniProfiler 的 7 个简单步骤

> 原文：<https://dev.to/simonech/7-easy-steps-to-install-miniprofiler-on-an-asp-net-mvc-website-1pp5>

今天我想把 [MiniProfiler](https://miniprofiler.com/dotnet/) 添加到一个 ASP.NET MVC web 应用程序(不是。NET Core)，但是不幸的是[的 NuGet 包](https://www.nuget.org/packages/MiniProfiler.Mvc5/)没有正确地设置东西，[的文档](https://miniprofiler.com/dotnet/AspDotNet)有点缺乏，并且[他们的示例项目](https://github.com/MiniProfiler/dotnet/tree/master/samples/Samples.Mvc5)不能工作，所以本来是一个简单的任务，花了几乎一整天的研究才使它工作。

在这篇文章中，我将用 7 个简单的步骤来解释如何在一个空的 ASP.NET MVC web 应用程序上安装 [MiniProfiler](https://miniprofiler.com/dotnet/) 。

### 步骤 1 -创建一个 ASP.NET MVC 应用程序

为了简单起见，让我们从创建一个新的 ASP.NET MVC 应用程序开始。

[![Choose ASP.NET Web Application Project](img/8c047e0084180959c41afdb6690f2b60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EnTYsyf_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://codeclimber.net.nz/media/1077/new-project.png)

[![Choose the MVC template](img/24b7413ccd5df5be4f87f06b715b6ae6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NhkOtwux--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://codeclimber.net.nz/media/1073/choose-template.png)

### 步骤 2 -安装微型分析器。MVC5 Nuget 包

要安装的正确 NuGet 包是`MiniProfiler.MVC5`，它提供了核心`MiniProfiler`包，并提供了与 ASP.NET MVC 的正确集成。

```
Install-Package MiniProfiler.Mvc5 
```

### 步骤 3 -添加最小微型分析器配置

配置发生在`Global.asax.cs`文件内的`Application_Start`、`Application_BeginRequest`和`Application_EndRequest`中。

在`Application_Start`中，你必须添加下面一行来设置默认选项。

```
MiniProfiler.Configure(new MiniProfilerOptions()); 
```

此外，然后添加`Application_BeginRequest`和`Application_EndRequest`方法，在每次请求时开始和停止跟踪。

```
protected void Application_BeginRequest()
{
    MiniProfiler profiler = null;
    if (Request.IsLocal)
    {
        profiler = MiniProfiler.StartNew();
    }
}

protected void Application_EndRequest()
{
    MiniProfiler.Current?.Stop();
} 
```

### 步骤 4 -在视图中包含脚本

现在您需要在视图中包含脚本。最简单的方法是在`Shared/_Layout.cshtml`主视图中添加它们。

在顶部，添加名称空间`using`声明`@using StackExchange.Profiling;`，在底部，就在`</body>`T3 之前

```
 @MiniProfiler.Current.RenderIncludes()
</body> 
```

### 步骤 5 -配置站点，将所有请求作为托管代码进行路由

如果你现在运行这个网站，什么也不会发生。原因是 MiniProfiler 依赖于当前呈现的 JavaScript 库，它被请求为`.js`，所以通常它不会被执行为。净请求。所以，你必须在`web.config`中添加下面一行。

```
<system.webServer>
   <modules runAllManagedModulesForAllRequests="true" />
 </system.webServer> 
```

现在运行网站，你会在网站的左上角看到 MiniProfiler 用户界面，点击它你会看到页面的执行时间。

[![Basic profiling information](img/99e199768afe42f30b118a588d1af9e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BsWdSjR3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://codeclimber.net.nz/media/1074/miniprofiler-ui-1.png)

### 步骤 6 -配置跟踪 MVC 控制器

当前视图不是很有用，因为除了整体执行之外，它什么也没有显示。在 ASP.NET MVC 应用程序中，通过在管道中添加一个新的过滤器，您可以开始跟踪每个动作的执行时间的更多信息。打开`FilterConfig.cs`文件(如果你跟随基本模板),将这一行添加到`RegisterGlobalFilters`方法

```
filters.Add(new ProfilingActionFilter()); 
```

现在，重新运行同一个站点，您还会看到控制器的执行时间。

[![Profiling Action and Controllers](img/3af1ff1d529ee8c8492f5073f4f35aed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H7cTjWYZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://codeclimber.net.nz/media/1075/miniprofiler-ui-2.png)

### 第 7 步-开始使用它

没有真正的第七步...但是有 7 步就很酷了。

玩笑归玩笑，既然基本配置已经工作了，你可以去官方文档看看[如何以一种重要的方式开始剖析你的](https://miniprofiler.com/dotnet/HowTo/ProfileCode)部分代码，通常是通过`Step`和`CustomTiming`方法完成的。

这里有一些示例代码，您可以将它们添加到您的操作中进行一些实验。

```
public ActionResult Index()
{
    var profiler = MiniProfiler.Current;
    using (profiler.Step("Set page title"))
    {
        ViewBag.Title = "Home Page";
    }

    using (profiler.Step("Doing complex stuff"))
    {
        using (profiler.Step("Step A"))
        {
            // simulate fetching a url
            using (profiler.CustomTiming("http", "GET http://google.com"))
            {
                Thread.Sleep(10);
            }
        }
        using (profiler.Step("Step B"))
        {
            // simulate fetching a url
            using (profiler.CustomTiming("http", "GET http://stackoverflow.com"))
            {
                Thread.Sleep(20);
            }

            using (profiler.CustomTiming("redis", "SET \"mykey\" 10"))
            {
                Thread.Sleep(5);
            }
        }
    }

    // now something that loops
    for (int i = 0; i < 15; i++)
    {
        using (profiler.CustomTiming("redis", "SET \"mykey\" 10"))
        {
            Thread.Sleep(i);
        }
    }

    return View();
} 
```

该代码生成下图所示的跟踪。

[![Warnings and categories](img/12b0153034c0f02dda8357ec4a8277d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_s-wNUea--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://codeclimber.net.nz/media/1076/miniprofiler-ui-3.png)

您可以注意到 MiniProfiler 检测到一些有趣的信息。它检测到代码调用了相同的 15 次(action 方法底部的循环)，以及有多少时间花费在不同类别的操作上(在本例中为`http`和`redis`)。

要添加的一个额外特性是使用 EF6 集成来跟踪 SQL 语句。
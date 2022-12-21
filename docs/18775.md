# 构建. NET Core 3 计划作业工人服务

> 原文：<https://dev.to/jamesmh/building-a-net-core-scheduled-job-worker-service-376h>

的。NET Core CLI 附带了大量预构建的项目模板！将包含在[中的新模板之一。网络核心 3](https://docs.microsoft.com/en-us/dotnet/core/whats-new/dotnet-core-3-0) 将用于建筑工人服务。

结合。NET Core worker services with[Cora vel](https://github.com/jamesmh/coravel)可以帮助您非常快速地构建轻量级后台作业调度应用程序。让我们来看看如何在短短几分钟内做到这一点！

*注意:工作者服务是轻量级的控制台应用程序，它执行某种类型的后台工作，如从队列中读取和处理工作(如发送电子邮件)，从我们的系统中执行一些预定的后台作业，等等。这些可能作为守护程序、windows 服务等运行。*

# 正在安装。网络核心 3 预览版

在写这篇文章的时候。网络核心 3 在预览中。首先，你必须[安装 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) 。本文中的其他内容都可以使用 Visual Studio 代码👍。

# Coravel 的任务调度

Coravel 是一个. NET 核心库，它以接近零的配置为您提供开箱即用的高级应用功能。[我受到了 Laravel 易用性的启发](https://www.blog.jamesmichaelhickey.com/What-I-ve-Learned-So-Far-Building-Coravel-Open-Source-NET-Core-Tooling/)，并希望将这种简单易用的构建 web 应用程序的方法引入到。网芯。

其中一个特性是 100%由代码配置的任务调度器。

通过利用 Coravel 的易用性和。NET Core 的 worker service 项目模板，我将向您展示如何轻松快速地构建一个小型后端控制台应用程序来运行您预定的后台作业！

# 工人服务模板

首先，创建一个空文件夹来存放您的新项目。

然后运行:

`dotnet new worker`

您的工人项目已经准备就绪！🤜🤛

查看一下 *Program.cs* ，你会看到这个:

```
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureServices(services =>
        {
            services.AddHostedService<Worker>();
        }); 
```

Enter fullscreen mode Exit fullscreen mode

# 配置 Coravel

让我们通过运行`dotnet add package coravel`来添加 Coravel。

接下来，在 *Program.cs* 中，我们将修改为我们生成的通用代码，并配置 Coravel:

```
public static void Main(string[] args)
{
    IHost host = CreateHostBuilder(args).Build();
    host.Services.UseScheduler(scheduler => {
        // We'll fill this in later ;)
    });
    host.Run();
}

public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureServices(services =>
        {
            services.AddScheduler();
        });
}; 
```

Enter fullscreen mode Exit fullscreen mode

因为科拉韦尔是本地人。NET 核心工具集，它*只是工作*与零大惊小怪！

# 增加可开发票

Coravel 的一个基本概念是[invoke als](https://docs.coravel.net/Invocables/)。

每一个 invocable 都代表系统中一个独立的任务，Coravel 利用它使你的代码更容易编写、合成和维护。

接下来，创建一个实现`Coravel.Invocable.IInvocable` :
的类

```
public class MyFirstInvocable : IInvocable
{
    public Task Invoke()
    {
        Console.WriteLine("This is my first invocable!");
        return Task.CompletedTask;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

由于我们将模拟一些异步工作，我们将只在控制台记录一条消息，然后将`Task.CompletedTask`返回给调用者。

# 调度您的可调用

这是 Coravel 真正闪光的地方😉。

让我们安排新的 invocable 每 5 秒钟运行一次。在我们的 *Program.cs* main 方法中，我们将添加:

```
host.Services.UseScheduler(scheduler => {
    // Yes, it's this easy!
    scheduler
        .Schedule<MyFirstInvocable>()
        .EveryFiveSeconds();
}); 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记向注册您的发票。NET Core 的服务容器:

```
.ConfigureServices(services =>
{
    services.AddScheduler();
    // Add this 👇
    services.AddTransient<MyFirstInvocable>();
}); 
```

Enter fullscreen mode Exit fullscreen mode

在您的终端中，运行`dotnet run`。

您应该每五秒钟就会在终端中看到输出！

# 现实世界可调用

当然，编写控制台是很棒的——但是您将进行 API 调用、数据库查询等。毕竟。

让我们修改我们的 invocable，这样我们可以做一些更有趣的事情:

```
public class SendDailyReportEmailJob : IInvocable
{
    private IMailer _mailer;
    private IUserRepository _repo;

    public SendDailyReportEmailJob(IMailer mailer, IUserRepository repo)
    {
        this._mailer = mailer;
        this._repo = repo;
    }

    public async Task Invoke()
    {
        var users = await this._repo.GetUsersAsync();

        foreach(var user in users)
        {
            var mailable = new DailyReportMailable(user);
            await this._mailer.SendAsync(mailable);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为这个类会挂钩到。NET 核心的服务容器，所有的构造函数依赖将通过依赖注入来注入。

如果您想要构建一个轻量级的后台应用程序，为您的所有用户处理并通过电子邮件发送每日报告，那么这可能是一个很好的选择。

# 配置为 Windows 服务

虽然超出了本文的范围，但是您可以看看如何实现。NET Core 3 将[允许将你的工人配置为 windows 服务](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/windows-service?view=aspnetcore-3.0&tabs=visual-studio-code#app-configuration)。

而且，很明显，[对 systemd 的支持也即将到来！](https://github.com/aspnet/Extensions/pull/1804)

# 结论

你们觉得。NET Core 的工人服务？

我发现它们很容易启动和运行。再加上 Coravel 中设计的可访问性，我发现这两者是做一些很酷的事情的绝佳组合！

Coravel 的所有特性都可以在这些工人服务中使用——比如[排队任务](https://docs.coravel.net/Queuing/)、[事件广播](https://docs.coravel.net/Events/)、[邮件](https://docs.coravel.net/Mailing/)等。

我想尝试的一件事是将 Coravel Pro 与工人服务集成。一步一步来🤣。

# 保持联系

不要忘记通过以下方式与我联系:

*   [推特](https://twitter.com/jamesmh_dev)
*   [LinkedIn](https://www.linkedin.com/in/jamesmhickey/)

你也可以在我的网站[www.jamesmichaelhickey.com](https://www.jamesmichaelhickey.com)找到我。

# 浏览您的软件开发职业简讯

一封电子邮件简讯，将帮助您提升软件开发人员的职业水平！有没有想过:

✔:软件开发人员的一般阶段是什么？✔:我怎么知道自己处于哪个阶段？我如何进入下一阶段？
✔什么是技术领导者，我如何成为一名技术领导者？✔，有人愿意和我一起走走，回答我的问题吗？

听起来有趣吗？加入社区吧！
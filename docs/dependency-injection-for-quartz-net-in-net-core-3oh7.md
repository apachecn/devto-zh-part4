# 年 Quartz.NET 的依赖注入。网络核心

> 原文：<https://dev.to/bohdanstupak1/dependency-injection-for-quartz-net-in-net-core-3oh7>

这篇文章最初发表在 [codeproject](https://www.codeproject.com/Articles/1277745/Dependency-Injection-for-Quartz-NET-in-NET-Core)

## 简介

Quartz.NET 是一个方便的库，它允许你通过实现`IJob`接口来安排周期性任务。然而，它的局限性在于，默认情况下，它只支持无参数的构造函数，这使得在其中注入外部服务变得复杂，例如，为了实现[存储库模式](https://www.codeproject.com/Questions/1267495/How-to-implement-repository-pattern-to-quartz-job)。在本文中，我们将看看如何使用标准来解决这个问题。NET Core DI 容器。

文章中提到的整个项目在下面的 [Github 仓库](https://github.com/Wkalmar/QuartzDI.Demo)中提供。为了更好地理解本文中的代码，您可能想看一看它。

## 项目概述

我们来看看初始解的结构。

项目`QuartzDI.Demo.External.DemoService`代表了一些我们无法控制的外部依赖。为了简单起见，它做了一件相当不起眼的工作。

项目`QuartzDI.Demo`是我们的工作项目，其中包含简单的 Quartz.NET 工作。

```
public class DemoJob : IJob
{
    private const string Url = "https://i.ua";

    public static IDemoService DemoService { get; set; }

    public Task Execute(IJobExecutionContext context)
    {
        DemoService.DoTask(Url);
        return Task.CompletedTask;
    }
} 
```

它是以一种简单的方式建立的:

```
var props = new NameValueCollection
{
    { "quartz.serializer.type", "binary" }
};
var factory = new StdSchedulerFactory(props);
var sched = await factory.GetScheduler();
await sched.Start();
var job = JobBuilder.Create<DemoJob>()
    .WithIdentity("myJob", "group1")
    .Build();
var trigger = TriggerBuilder.Create()
    .WithIdentity("myTrigger", "group1")
    .StartNow()
    .WithSimpleSchedule(x => x
        .WithIntervalInSeconds(5)
        .RepeatForever())
.Build();
await sched.ScheduleJob(job, trigger); 
```

我们通过 job 的`static`属性提供我们的外部服务

```
DemoJob.DemoService = new DemoService(); 
```

由于该项目是一个控制台应用程序，在本文的过程中，我们将不得不手动安装所有需要的基础设施，并将能够更彻底地了解实际上是什么。NET Core 带给我们的。

至此，我们的项目已经启动并运行。最重要的是，非常简单也很棒。但是我们为这种简单性付出了应用程序不灵活的代价，如果我们想把它作为一个小工具，这是很好的。但是对于生产系统来说，情况往往不是这样。因此，让我们稍微调整一下，使它更加灵活。

## 创建配置文件

其中一个不灵活的地方是我们将 URL 硬编码到一个`DemoJob`中。理想情况下，我们希望改变它，并且根据我们的环境来改变它。。NET Core 附带了 appsettings.json 机制。

为了开始工作。NET 核心配置机制，我们必须安装几个 Nuget 包:

```
Microsoft.Extensions.Configuration
Microsoft.Extensions.Configuration.FileExtensions
Microsoft.Extensions.Configuration.Json 
```

让我们用这样的名称创建一个文件，并在那里提取我们的 URL:

```
{
  "connection": {
    "Url": "https://i.ua"
  }
} 
```

现在我们可以从配置文件中提取我们的值，如下所示:

```
var builder = new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", true, true);
var configuration = builder.Build();
var connectionSection = configuration.GetSection("connection");
DemoJob.Url = connectionSection["Url"]; 
```

请注意，要实现这一点，我们必须将 Url 从常量改为属性。

```
public static string Url { get; set; } 
```

## 使用构造函数注入

通过一个`static`属性注入服务对于一个简单的项目来说是很好的，但是对于一个更大的项目来说，它可能会带来几个缺点:比如可能在没有提供服务的情况下调用作业，从而在对象运行时失败或者改变依赖关系，这使得推理对象变得更加困难。为了解决这些问题，我们应该使用构造函数注入。

尽管纯依赖注入没有任何问题，并且有些人认为您应该在本文中努力实现它，但是我们将使用内置的。NET Core DI 容器，它带有一个 Nuget 包`Microsoft.Extensions.DependencyInjection`。

现在，我们在构造函数参数中指定我们依赖的服务:

```
private readonly IDemoService _demoService;

public DemoJob(IDemoService demoService)
{
    _demoService = demoService;
} 
```

为了调用作业的参数化构造函数，Quartz.NET 提供了 IJobFactory 接口。下面是我们的实现:

```
public class DemoJobFactory : IJobFactory
{
    private readonly IServiceProvider _serviceProvider;

    public DemoJobFactory(IServiceProvider serviceProvider)
    {
        _serviceProvider = serviceProvider;
    }

    public IJob NewJob(TriggerFiredBundle bundle, IScheduler scheduler)
    {
        return _serviceProvider.GetService<DemoJob>();
    }

    public void ReturnJob(IJob job)
    {
        var disposable = job as IDisposable;
        disposable?.Dispose();
    }
} 
```

让我们注册我们的依赖关系:

```
var serviceCollection = new ServiceCollection();
serviceCollection.AddScoped<DemoJob>();
serviceCollection.AddScoped<IDemoService, DemoService>();
var serviceProvider = serviceCollection.BuildServiceProvider(); 
```

拼图的最后一块是让 Quartz.NET 使用我们的工厂。IScheduler 有一个房地产工厂就是为了这个。

```
sched.JobFactory = new DemoJobFactory(serviceProvider); 
```

## 使用选项模式

现在我们可以用配置选项来玩同样的把戏。同样，我们的例程从一个 Nuget 包开始。这次`Microsoft.Extensions.Options`。

让我们为配置选项创建一个强类型定义:

```
public class DemoJobOptions
{
    public string Url { get; set; }
} 
```

现在我们按如下方式填充它们:

```
serviceCollection.AddOptions();
serviceCollection.Configure<DemoJobOptions>(options =>
{
    options.Url = connectionSection["Url"];
}); 
```

并将它们注入到构造函数中。不是说我们直接注入选项，而不是选项实例。

```
public DemoJob(IDemoService demoService, IOptions<DemoJobOptions> options)
{
    _demoService = demoService;
    _options = options.Value;
} 
```

## 结论

在本文中，我们看到了如何利用。NET 核心功能，使我们对 Quartz.NET 的使用更加灵活。
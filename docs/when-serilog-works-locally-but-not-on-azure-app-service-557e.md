# 当 Serilog 在本地工作，但不在 Azure 应用服务上工作时

> 原文：<https://dev.to/andbe/when-serilog-works-locally-but-not-on-azure-app-service-557e>

这真的让我很困惑，我花了很多时间试图理解一些非常简单和有逻辑的东西。你不讨厌这些问题吗？

我正在开发的一个应用程序正在使用 [Serilog](https://serilog.com) 。Serilog 很牛逼。该应用运行 ASP.NET 核心 2.2，托管在 Azure 应用服务上。

让 Serilog 在本地工作很容易，只需用 Nuget 安装它，添加所需的接收器并在`Program.cs`
中配置它

```
public static void Main(string[] args) {
    // Create the Logger configuration
    Log.Logger = new LoggerConfiguration()
        .MinimumLevel.Debug()
        .MinimumLevel.Override("Microsoft", LogEventLevel.Warning)
        .WriteTo.Console()
        .CreateLogger();
    // ...
    try
    {
        Log.Debug("Application Starting at {date} ({EnvironmentName}).", DateTime.Now, EnvironmentName);
        CreateWebHostBuilder(args)
            .Build()
            .Run();
    }
    catch (Exception ex)
    {
        Log.Fatal(ex, "Application died");
    }
    finally
    {
        Log.CloseAndFlush();
    }
} 
```

然后只需添加`.UseSerilog()``IWebHostBuilder`方法`CreateWebHostBuilder`(假设你正在使用标准的. net CLI 初始化，或者 Visual Studio 创建的项目)。

然而，我希望 Serilog 从我的应用程序设置和环境变量中读取数据，以便使用 Azure portal 进行配置。添加`Serilog.Settings.Configuration`包可以解决这个问题，在`CreateLogger()`之前添加一个简单的`ReadFrom.Configuration()`

同样，这在我的本地开发环境中很好。然而，当构建和发布到 Azure 时，没有记录任何东西。

奇怪的是，在调用`CreateWebHostBuild`方法之前，Azure 似乎没有获取环境变量和/或配置。如果你想记录应用程序启动时可能发生的任何事情(比如，在实际的`CreateWebHostBuilder`中，或者甚至在那之前)，这是很麻烦的。

这里有一个简单的方法来解决它；用一个可以在 Azure 上访问的硬编码文件路径(例如`D:\home\LogFiles\Application\`)配置日志记录器，并在 Startup.cs ( `ConfigureServices`)中用*重新配置*。

```
public void ConfigureServices(IServiceCollection services)
{
    Log.Logger = new LoggerConfiguration()
        .MinimumLevel.Debug()
        .MinimumLevel.Override("Microsoft", LogEventLevel.Warning)
        .ReadFrom.Configuration(Configuration) // << THIS!!
        .Enrich.FromLogContext()
        .CreateLogger();
    // All other goodies 
} 
```

现在，在 mac 或没有 D:\的 Windows 上运行这个会带来新的问题。我通过检查一个我*知道的* Azure 设置的环境变量来解决这个问题:*网站名称*

下面是更新后的`Program.cs`主函数:

```
public static void Main(string[] args)
{
    // Check if app is running on Azure, the WEBSITE_SITE_NAME environment variable will be set if it is.
    if (!string.IsNullOrEmpty(Environment.GetEnvironmentVariable("WEBSITE_SITE_NAME")))
    {
        Log.Logger = new LoggerConfiguration()
            .MinimumLevel.Information()
            .Enrich.FromLogContext()
            .WriteTo.File(path: @"D:\home\LogFiles\Application\log.txt", fileSizeLimitBytes: 1_000_000,
                flushToDiskInterval: TimeSpan.FromSeconds(5), shared: true,
                restrictedToMinimumLevel: LogEventLevel.Debug)
            .CreateLogger();
    }
    else
    {
        // Not Azure, just log to Console, no need to persist
        Log.Logger = new LoggerConfiguration()
            .MinimumLevel.Debug()
            .MinimumLevel.Override("Microsoft", LogEventLevel.Warning)
            .WriteTo.Console()
            .CreateLogger();
    }

    try
    {
        Log.Debug("Application Starting at {date} ({EnvironmentName}).", DateTime.Now, EnvironmentName);
        CreateWebHostBuilder(args)
            .Build()
            .Run();
    }
    catch (Exception ex)
    {
        Log.Fatal(ex, "Application died 💀");
    }
    finally
    {
        Log.CloseAndFlush();
    }
} 
```
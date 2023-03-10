# 。NET 框架到。网络核心到 Docker

> 原文：<https://dev.to/magnusstrale/net-framework-to-net-core-to-docker-3m3b>

这是从现有的。NET 框架的 REST-ful 服务并让它在。Docker 容器中的 NET Core。

我很幸运地从一个构建 TDD 风格的服务开始，这意味着有很好的测试覆盖率和一个总体上设计得很好的具有适当抽象的应用程序。这意味着我相当有信心，如果我能让所有的测试都是绿色的，那么我就有了一个可行的解决方案。

该服务基于 Nancy(“轻量级、简单的框架，用于在其上构建基于 HTTP 的服务。Net”)并使用 top shelf(“win service 开发入门的最简单方法”)将代码作为 Windows 服务运行。两者都是过时的项目，TopShelf 根本不适用于 Linux。决定继续由南希主持，由凯斯特瑞尔主持。即使没有官方的 Nancy 版本支持。NET Core 有一个预发布版本支持。NET 标准 2.0。

。网标 vs .网芯 vs“老”。NET Framework 是很容易让你迷惑的东西。我不会试图在这里解释什么是什么，而是让你参考这篇文章[https://www . infoq . com/news/2017/10/dot net-core-standard-difference](https://www.infoq.com/news/2017/10/dotnet-core-standard-difference)，它很好地解释了这些概念。

如果你想让你的代码在 Linux / Docker 上运行，基本原则是你的项目要有目标。NET 标准，并且。NET Core 用于以下情况。净标准是不够的。稍后会有更多的介绍。

* * *

### 入门

自以来，项目文件已针对。NET Core，主要描述惯例的例外情况，而不是详细描述项目中应该包含的每一件小事，简单地扔掉所有现有的 csproj 文件并从头开始使用这个模板是有意义的:

```
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
</Project> 
```

Enter fullscreen mode Exit fullscreen mode

然后继续添加 NuGet 和项目引用，直到用完"[CS0246]找不到类型或命名空间名称' Xxx '(是否缺少 using 指令或程序集引用？)".我需要的大多数软件包都可以在。NET 标准版，所以这是一个相当轻松的过程(至少与我预期的相比)。两个主要问题是嵌入式资源和应用程序设置。这两个都没有真正用老式的方法很好地实现。NET Framework，并且已经进行了重大修改(在配置设置的情况下)，或者干脆不支持/删除(在 Resx 文件的情况下)。

无论如何，随着 csproj 文件的转换和一些注释代码，整个事情都编译好了。真正好的部分是 csproj 文件的大小**显著**缩小了。3756 行的最大文件已经减少到只有 38 行。这样做的主要原因是约定和适当的通配符支持——这是杀死 3000+行的原因:

```
<ItemGroup>
  <None Update="TC\TC*\**">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
  </None>
  <None Update="TCData\**">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
  </None>
</ItemGroup> 
```

Enter fullscreen mode Exit fullscreen mode

这也使得 csproj 文件可以作为纯文本/ XML 文件在编辑器中使用，而不仅仅是让它们作为 IDE 中交互的结果进行隐式更新。

### 资源和 Resx 文件

其中两个项目包含嵌入式资源。这只是一种将非代码内容放入编译器生成的程序集中的方法，比如文件、本地化的文本字符串、图标等。英寸这由 IDE 处理，它创建一个包含信息的. resx 文件，并自动生成一个 Resources 类，该类为您提供对资源的完全类型化访问。这个过程多半是魔术。

转换为时。NET Core，Resx 文件仍然在那里，JetBrains Rider(我在 Visual Studio 上使用它，原因很简单，它包含在我拥有的 IntelliJ 许可证中)几乎可以让它工作。但是，这个过程会引入旧的程序集来读取和解析 Resx 文件，以确定如何解析其他嵌入的资源。在花了几个小时试图让服务访问嵌入式文件(让它工作)并仍然能够从 IDE 获得良好的编辑体验(让它工作，但破坏了代码)之后，我放弃了 Resx，并决定通过直接访问 ResourceManager 来实现，resource manager 是负责处理嵌入式资源的类。事实证明这非常容易，部分原因是我需要的唯一资源类型是文件。这段代码正是我所需要的，而不是试图与向后兼容性作斗争:

```
public static class Resources 
{
    private static readonly string _prefix = typeof(Resources).FullName + ".";
    private static readonly Assembly _assembly = typeof(Resources).Assembly;
    public static Stream GetStream(string name)
    {
        return _assembly.GetManifestResourceStream(_prefix + name);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 配置设置

经典的。NET Framework 中，您通常有自己的 app.config 或 web.config，其中包含应用程序的所有配置设置。任何自定义设置都可以放在节下，或者您可以编写自己的完全类型化的配置节处理程序。然而在。NET Core“官方”的方式更加可定制，你可以选择从哪里获取配置设置(配置文件，环境变量，命令行参数等)。然而，我只是想用尽可能少的工作得到的东西。看一下 NuGet feeds，它实际上是一个系统。支持的配置包。NET Standard 2.0，最初并不是这样。

Owin / Kestrel 的配置还存在一些问题——看起来使用了新的配置系统，为 Kestrel 提供了更清晰的界面。也考虑到我最近的资源经验，所以我决定切换到新的配置系统。这几乎和资源部分一样简单。

```
public static IWebHost BuildWebHost(string[] args)
    {
        var builder = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .AddEnvironmentVariables();
        var configuration = builder.Build();
        return WebHost.CreateDefaultBuilder(args)
            .UseConfiguration(configuration)
            .UseUrls($"http://*:{configuration["app:serverPort"]}")
            .UseStartup<Startup>()
            .Build();
    } 
```

Enter fullscreen mode Exit fullscreen mode

以上其实是先睹为快除了配置处理之外的红隼配置。上面的代码与这个 appsettings.json 文件放在一起:

```
{
  "app": {
    "ApiUrl": "http://127.0.0.1:10010",
    "serverPort": "8096",
    "ProgressEventBusHost": "192.168.32.128",
    "ProgressEventExchangeName": "dev.global.exchange",
    "MaxParallellism": "1",
    "QueueCapacity": "1000",
    "HangSafeGuardTimeSeconds": "300"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 托管在红隼和 OWIN

嗯——在前面的代码片段中，您已经看到了它的一部分。如前所述，nancy 的预发布版本支持。净标准。它有一个不祥的名字 Nancy 2.0.0-clinteastwood，Nancy 的大部分旧配置代码(按照下面引用的 CustomBootstrapper)原封不动地工作。真正需要的是我们在前面的 BuildWebHost 方法中提到的启动类:

```
 class Startup
    {
        private readonly IConfiguration _configuration;

        public Startup(IConfiguration configuration)
        {
            _configuration = configuration;
        }

       public void Configure(IApplicationBuilder app, IHostingEnvironment env)
       {
           Console.WriteLine(env.ContentRootPath);
           Console.WriteLine(Directory.GetCurrentDirectory());

            var appSettings = new AppSettingsProvider(_configuration);
            app.UseOwin(x => x.UseNancy(b =>
            {
               b.Bootstrapper = new CustomBootstrapper(appSettings);
            }));
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

### 来自命令行的 Dotnet

在把东西放到 Docker 上之前，我确保我已经在 Windows 命令行上用 dotnet 命令进行了构建和测试。到目前为止，我一直都是在 Rider 内部运行一切。把这个小脚本放在一起，我把它全部编译并测试绿色:

```
dotnet build src/App.sln -c Release
forfiles /p .\src /m *Tests /c "cmd /c dotnet test --no-build -c Release @path"
dotnet publish -c Release -o out -f netcoreapp2.0 src/AppHost/AppHost.csproj 
```

Enter fullscreen mode Exit fullscreen mode

现在进行最后的测试——启动服务，看看它是否如预期的那样响应。从解决方案发布到的 out 文件夹中执行以下命令:

```
dotnet AppHost.dll 
```

Enter fullscreen mode Exit fullscreen mode

运气不好-"遇到了致命错误。在“…AppHost\out\”中找不到执行应用程序所需的库“hostpolicy.dll”。
无法作为独立的应用程序运行。如果这应该是一个依赖于框架的应用程序，请添加…AppHost \ out \ AppHost . runtime config . JSON 文件来指定适当的框架。至少这是一个非常好的错误消息，一个 AppHost.runtimeconfig.json 出现了！

```
{
  "runtimeOptions": {
    "framework": {
      "name": "Microsoft.NETCore.App",
      "version": "2.0.0"
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

是啊！它跑了！将浏览器指向 [http://localhost:9922](http://localhost:9922) 确实会产生预期的响应。

### 放入 Docker

既然我已经完成了所有的艰苦工作，我以为这是一件轻而易举的事。嗯，不完全是……它开始得很好，利用我高超的谷歌技能，我找到了一个看起来很好的起点[https://docs.docker.com/engine/examples/dotnetcore/](https://docs.docker.com/engine/examples/dotnetcore/)
我从文章中的 Dockerfile 开始，并稍微调整它，使服务在容器中构建并运行测试:

```
FROM microsoft/dotnet:sdk AS build-env
WORKDIR /app/src
COPY . .
RUN dotnet restore
RUN dotnet test -c Release DomainModel.Tests/DomainModel.Tests.csproj
RUN dotnet test -c Release App.Services.Tests/App.Services.Tests.csproj
RUN dotnet test -c Release App.Tests/App.Tests.csproj 
```

Enter fullscreen mode Exit fullscreen mode

领域模型非常有效，都是绿色的，但是其他两个项目开始向我抛出大量的测试失败列表。事实证明(几乎)所有的问题都有一个共同点...

### 斜线 vs 反斜杠

我开始写 PC-DOS 和它的历史背景，只是为了炫耀我的年龄和经历，但我决定不写了。如果你对背景感兴趣的话，可以看看这篇小文章[https://www . how togeek . com/181774/why-windows-uses-back slashs-and-everything-else-uses-forward-slashs/](https://www.howtogeek.com/181774/why-windows-uses-backslashes-and-everything-else-uses-forward-slashes/)

该服务相当广泛地使用文件，将数据作为用 protobuf 序列化的文件来保存。这意味着反斜杠作为文件夹分隔符出现在代码的许多部分。正如我已经暗示的，当在 Docker 下运行时，这立即破坏了几乎所有与文件相关的测试。解决这个问题很容易，因为 Windows 实际上接受正斜杠作为文件夹分隔符。

写作时。NET 代码。NET 标准/。NET Core，始终使用“/”作为文件夹分隔符。甚至更好——使用路径。从路径开始组合添加文件夹名称。Combine 将根据您的操作系统使用正确的分离器。最后一部分是不要使用绝对路径(比如 C:\temp，甚至写成 C:/temp 在 Linux 上没有意义)。

### 时区和码头集装箱

一旦我解决了斜杠 vs 反斜杠的失败，我还有一个红色测试要处理。它基本上是通过 Nancy Browser 发送一个查询，并期望返回的数据与一个 JSON 字符串完全匹配。不幸的是它没有，它显示了这个:

```
Failed   ThenTheJsonBodyIsSerializedAsBefore
Error Message:
   String lengths are both 742\. Strings differ at index 178.
  Expected: "..."0001-01-01T00:00:00.0000000+01:00","endTime":"0001-01-01T..."
  But was:  "..."0001-01-01T00:00:00.0000000+00:00","endTime":"0001-01-01T..."
  --------------------------------------------^ 
```

Enter fullscreen mode Exit fullscreen mode

这个测试是由在 UTC+1 时区的人写的，因为我在斯德哥尔摩(UTC+1)，所以它“在我的机器上工作”。然而，似乎大多数可用于生产工作负载的 Docker 容器都是为 UTC 和许多(大多数？)案例只是没有可配置的时区，因为缺少/usr/share/zoneinfo。在 IMO 看来，这非常有意义——无论 docker 容器部署在哪里，它的行为都应该是一样的，因此时区特定的设置是不相关的。

我确实认为测试有点可疑——这些类型的“重放”测试是脆弱的，我更喜欢验证实际数据，而不是序列化的表示。这个经验法则的一个例外是，如果您需要确保持久化数据的向后兼容性。

重写这个测试在我的待办事项中，但是现在我只是调整了测试验证来忽略时区差异。嘿，我并不完美…

### 最终对接件

使用“dotnet publish”命令将所有必要的文件放在一个地方进行最终部署，并将其放在 out 文件夹中。在这种情况下，定义使用哪个框架版本的-f 标志是必需的，但是文档似乎暗示这可能是可选的。我怀疑这与我将不同项目的目标混在一起的事实有关。

真正有趣的方面是 docker 文件利用了多阶段构建。这仅仅意味着我可以在我的 docker 文件中添加一个 FROM 语句，它将创建一个新的映像，可以使用 docker 文件中以前阶段的文件。请注意，第一个来自引用 microsoft/dotnet:sdk 映像，而第二个来自使用 Microsoft/dot net:aspnetcore-runtime，这是一个非常小的映像。最后，我们将 publish 命令的输出复制到我们的新图像上，并设置一个入口点。也就是说，运行时映像将只包含运行我们的应用程序的最低限度，没有其他工具或非运行时构建工件。

有关多阶段构建的更多信息，请参见[http://recurse.se/2017/09/docker-multistage-builds/](http://recurse.se/2017/09/docker-multistage-builds/)

```
FROM microsoft/dotnet:sdk AS build-env
WORKDIR /app/src
# Copy and restore as distinct layers
COPY . .
RUN dotnet restore
RUN dotnet test -c Release DomainModel.Tests/DomainModel.Tests.csproj
RUN dotnet test -c Release App.Services.Tests/App.Services.Tests.csproj
RUN dotnet test -c Release App.Tests/App.Tests.csproj
RUN dotnet publish -c Release -o out -f netcoreapp2.0 AppHost/AppHost.csproj
# Build runtime image
FROM microsoft/dotnet:aspnetcore-runtime
WORKDIR /app
COPY --from=build-env /app/src/AppHost/out .
ENTRYPOINT ["dotnet", "AppHost.dll"] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 吸取教训

*   不要为了节省重写一两个类的工作而引入兼容性的东西。最终的结果将更干净，功能更强大(看看你的 Resx 文件和系统。配置)。
*   目标。尽可能使用. NET 标准。它允许您的代码在旧的。NET 框架和。网芯。
*   使用多级构建来创建小型 Docker 容器。
*   不要用“\”硬编码路径——在大多数情况下，Windows 对“/”完全满意。甚至更好——使用路径。根据您的平台组合使用正确的分隔符。
*   站在巨人的肩膀上。并不是我在这里写的所有东西都被你解决了。真的，我很幸运能够看到我的同事 Martin Hellspong 经历了类似处理的另一个项目。点击这里查看 http://recurse.se/的博客
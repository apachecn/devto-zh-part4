# 利用 PostSharp、Application Insights 和 Grafana 进行异常处理和遥测

> 原文：<https://dev.to/dealeron/exception-handling-and-telemetry-with-postsharp-application-insights-and-grafana-5bie>

<figcaption>PostSharp is a pattern-aware extension for C# which runs during the compilation stage.</figcaption>

不管你是刚刚开始。NET，或者一直在使用它，但是每个项目最终都要面对一些问题。在上一篇文章中，我讨论了如何使用 PostSharp 解决日志和线程等问题。今天，我将向您展示我们如何将这些相同的原则应用于异常处理，以便向应用洞察发送实时遥测数据。

### 备注

异常处理是一个深度远远超出本文范围的主题。我在这里引用的代码并不是为了尽可能快或尽可能高效而设计的。这是为了展示每个特性，并创建一个有用的演示，读者可以根据自己的目的对其进行改进。欢迎您随时与我联系，就这些话题进行澄清或提出建议；我也欢迎您指出错误，甚至在您认为合适的时候向存储库提出请求。

我的目标是探索。NET 核心跨平台功能，并向您展示如何将异常处理与以下工具集成:

[。NET Generic Host](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-2.2) 是一个一体化的包，用于处理配置、依赖注入、日志记录和其他应用程序启动问题。如果您想要托管异步服务，那么您现在就可以用很少的设置时间来完成它。对我们来说，这将简化许多原型化有趣的 [AOP](https://en.wikipedia.org/wiki/Aspect-oriented_programming) 用例所需的样板文件。

[Azure Application Insights](https://docs.microsoft.com/en-us/azure/azure-monitor/app/app-insights-overview) 是一个大型服务套件，专注于实时监控性能。你可以向 Azure 发送事件、指标和各种自定义测量，然后立即通过[日志分析](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/log-query-overview)进行查询。这里的目标是通过在云中记录异常来处理我们的异常，在云中我们可以获得应用程序状态的定期快照。

Grafana 是一款出色的[开源](https://github.com/grafana/grafana)工具，用于创建跟踪指标或将数据可视化为图表的仪表板。你可以自己托管，但也有免费和付费的云托管选项。免费选项是原型开发的理想选择，拥有我们现在需要的所有特性。

使用模式感知编译器的能力，这种横切关注点可以无缝地连接到外部监控服务，而无需在代码库中到处添加`try {} catch {}`块。这种不杂乱有助于保持代码清晰易懂，并减少团队的关注点。

### 我们开始之前

希望跟进的读者应该采取以下步骤来设置他们的环境:

*   下载[。NET Core SDK 和运行时](https://dotnet.microsoft.com/download)。我将在上使用 VS 2017。NET Core SDK 2.2.106
*   从 VS Marketplace 下载最新稳定的 PostSharp 扩展[。这里用的是 6.1.18 版本。](https://marketplace.visualstudio.com/items?itemName=PostSharpTechnologies.PostSharp)
*   克隆我的 SharpCrafting 库的语义分支或者按照我的[以前的文章](https://dev.to/sohjsolwin/automating-net-core-services-with-postsharp-and-aspect-oriented-code-349g-temp-slug-7775299)来构建你自己的线程化通用主机。

### 了解模式感知异常处理

异常处理程序通常要求您使用某种形式的`try { } catch { } finally { }`语句，这要求每个类都充分了解自己的上下文，以便要么冒泡、吞下，要么处理内部发生的异常。处理不当的异常很容易导致服务中断或最终用户体验不佳。这给任何开发团队带来了文档和知识的负担，以理解任何特定功能范围之外的整体错误处理架构。

例如，仅在一个解决方案中，我就发现了数百个`try { }`块的实例。如果您运行分布式远程工作器，这可能会很困难，因为异常可能需要跨越许多边界，而不会丢失其原始调用者和状态。在这数百个`try { }`块中，无疑会有一两个块由于业务需求的缓慢特性蔓延而无法按照最初的意图进行处理。如果这些需求发生了重大变化，那么任何一个处理程序都有可能因为兼容性而被忽略。这可能会导致吞咽异常或不可识别的调用堆栈。错误处理异常的可能性成为重构的主要障碍，因此对团队和项目经理来说是一个棘手的问题。

这就是面向方面的设计介入的地方，它通过将逻辑整合到一个公共的地方来提供解决方案。

[处理异常](https://doc.postsharp.net/exception-handling)是 PostSharp 名称空间的主要特性，我建议在继续之前阅读一下。以下是为我们提供的基本实现对象:

*   住在[波斯特夏普的](https://doc.postsharp.net/n_postsharp_aspects)[one exception aspect](https://doc.postsharp.net/t_postsharp_aspects_onexceptionaspect)班。看点。这在方法周围定义了一个异常处理程序，它将拦截任何抛出的异常，并可以在更高的级别处理它们。
*   [MethodExecutionArgs](https://doc.postsharp.net/t_postsharp_aspects_methodexecutionargs) 类，它为您的处理程序提供包含建议的参数。您可以使用这些参数来获取原始异常、原始调用者提供给被拦截方法的参数以及执行的流行为。

异常处理的官方 [PostSharp 示例是一个很好的资源，可以和任何文档一起开放。我将借用示例向您展示这种实现的一个实例。](https://samples.postsharp.net/f/PostSharp.Samples.ExceptionHandling)

第一个例子是“异常时添加上下文”[属性](https://samples.postsharp.net/f/PostSharp.Samples.ExceptionHandling/AddContextOnExceptionAttribute.cs.html)。当一个异常被这个属性截获时，它将把参数值附加到异常中。稍后在日志中阅读异常时访问参数值可以节省调试时间。如果你正在处理一个不容易重现的 bug，这是一个非常特别的特性。在很多情况下，如果我知道是哪些参数首先导致了异常，那会节省我很多时间。

这个类有几个有趣的地方:

*   首先，它继承了提供异常处理程序的 [OnExceptionAspect](https://doc.postsharp.net/t_postsharp_aspects_onexceptionaspect) 类。
*   接下来，它拦截用`OnException( MethodExecutionArgs args )`抛出的异常，并用前面提到的`MethodExecutionArgs`跟踪它们的状态。
*   最后，它构建一个包含上下文的字符串，包括参数、声明类型、泛型和方法名。这些被添加到[数据属性](https://docs.microsoft.com/en-us/dotnet/api/system.exception.data?view=netcore-2.2)的异常“上下文”索引中。
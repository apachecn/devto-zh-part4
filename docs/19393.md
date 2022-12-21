# 负载测试 ASP.NET 核心信号

> 原文：<https://dev.to/staff0rd/load-testing-asp-net-core-signalr-4pff>

[上次大约在](https://staffordwilliams.com/blog/2019/03/20/azure-signalr-service/)的时候，我在[负载测试](https://staffordwilliams.com/blog/2019/03/20/azure-signalr-service/#load-testing)上简单地碰了一下 SignalR。这一次，我将深入研究 SignalR 负载测试，特别是测试 source，Crankier 中提供的工具，并构建自己的负载测试工具来研究 SignalR 应用程序的局限性。

## 为什么？

我正在开发一个基于 SignalR 的应用程序，我打算用越来越多的人来逐步测试它。在这些真人测试之前，我希望对应用程序的连接限制和延迟预期有信心和了解。一个应用程序演示因负载而摔倒，而这本来可以用机器人来调查🤖而不是人🤦‍♀是我想跳过的一段经历。

三个月前，我开始对 SignalR 进行负载测试，这让我陷入了一个疯狂的学习兔子洞——这篇文章将总结这段旅程和发现。

## 脾气暴躁

[Crankier](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR/perf/benchmarkapps/Crankier) 是 Crank 的 ASP.NET 核心 SignalR 端口，是 ASP.NET SignalR 附带的负载测试工具。目前，Crankier 所做的唯一一件事就是试图打开与 SignalR hub 的并发连接。还有一个[基准服务器](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR/perf/benchmarkapps/BenchmarkServer)，我们可以用它来托管一个 SignalR hub 进行负载测试。

至少，我们可以克隆 aspnetcore repo 并运行这两个应用程序作为本地测试:

```
git clone https://github.com/aspnet/AspNetCore
cd aspnetcore/src/SignalR/perf/benchmarkapps 
```

启动服务器:

```
cd BenchmarkServer
dotnet run 
```

开始胡思乱想:

```
cd Crankier
dotnet run -- local --target-url http://localhost:5000/echo --workers 10 
```

我把服务器放在一个 Azure 应用服务上，也对它指指点点。这是结果。

| 计算机网络服务器 | 最大连接数 |
| --- | --- |
| 当地的 | Eight thousand one hundred and thirteen |
| B1 应用服务 | Three hundred and fifty |
| S1 应用服务 | Seven hundred and sixty-eight |
| P1V1 应用服务 | Seven hundred and sixty-eight |

这些结果看起来令人担忧，但背后有其道理。8113 是我的本地机器可以建立的最大 http 连接数，即使是在机器内部。但如果是这样的话，为什么我不能通过应用程序服务获得那个号码呢？对 B1 的限制在 [Azure 订阅服务限制](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits)上有说明，但同一页面上注明了对 S1 和 P1V1 的*网络套接字每个实例*的*无限制*。结果(通过 Azure 支持)768 是每个客户端的(未记录的)连接限制。我需要更多的客户！

## 在一个容器内承载胡思乱想

我想生成多个客户机来测试连接限制，因此容器似乎是一个很好的主意。将 Crankier 放在一个容器中相当容易，这里是我为此构建的 [Dockerfile](https://github.com/staff0rd/docker-crankier/blob/master/client/container/Dockerfile) 。我已经将它推送到 docker hub，所以我们可以跳过构建它，直接运行它。上面的`run`命令现在变成了:

```
docker run staff0rd/crankier --target-url http://myPath/echo --workers 10 
```

使用这种方法，我可以将应用服务推到 768 个并发连接以上，但我仍然需要每 768 个连接一个客户端。我想追求更高的数字，所以我将应用程序服务换成虚拟机，我将直接在虚拟机上运行基准服务器。

## 在容器内托管 BenchmarkServer

现在我有多个客户端，不再清楚我达到了多少个并发连接。我将扩展基准服务器以每秒回显一些重要信息:

*   我们运行的总时间
*   当前连接数
*   峰值连接数
*   最后一秒的新连接
*   最后一秒钟的新断开

我已经在这里分支了 aspnetcore 来实现上面的功能。

此外，我将把它放在一个容器中，这样我就不必在 VM 上安装它的任何依赖项，比如 dotnet sdk。这里是 [Dockerfile](https://github.com/staff0rd/docker-crankier/blob/master/server/Dockerfile) ，但是通常它在 docker hub 上，所以我们现在可以像这样启动服务器:

```
docker run -d -p 80:80 crankier staff0rd/crankier-server 
```

## 容器客户端结果

现在我们可以筹集 10-20 英镑(或者更多！)容器，其中`XX`每次递增:

```
az container create -g crankier --image staff0rd/crankier --cpu 1 --memory 1 \
    --command-line "--target-url http://myPath/echo --workers 10 --connections 20000" \
    --no-wait --name myContainerXX 
```

经过多次测试，我发现操作系统似乎没有太大的区别，所以我坚持使用 Linux，因为它更便宜。我没有发现用 docker 在容器中运行服务器与通过安装在 VM 上的`dotnet`直接运行服务器有什么不同，所以未来的测试坚持只在 docker 中运行服务器。

结果如下:

| VmSize | 最大连接数 | 时间 | 操作系统（Operating System） |
| --- | --- | --- | --- |
| B2s | Sixty-four thousand two hundred | 15 米 | Windows 数据中心 2019 |
| B2s | Sixty-four thousand nine hundred and fifty-seven | 18 米 | Windows 数据中心 2019 |
| B2s | Fifty-seven thousand four hundred and thirty-six | > 5m | Ubuntu 16.04 |
| B2ms | One hundred and seven thousand nine hundred and forty-four | 7m | Ubuntu 16.04 (50 多个容器) |

总的来说，这些结果比我预期的要低一点，并且仍然存在两个问题。

1.  默认情况下，Crankier 在断开连接之前只保持连接打开 5 分钟。任何运行超过 5 分钟的测试都会断开连接；
2.  一些容器只允许 500 个并发连接。如果我举起 10 个集装箱，其中只有 1 或 2 个能超过 500。

第一个问题很容易解决，通过传递`--send-duration 10000`保持连接打开 10000 秒，但是第二个问题需要迁移到虚拟机作为客户端。

## 虚拟机上运行的 Crankier

我发现虚拟机在建立许多连接方面要可靠得多，但我的问题是它们不像容器那样容易自动化。所以，我[用这些脚本自己](https://github.com/staff0rd/docker-crankier/tree/master/client/vm)构建了自动化:

*   [clientVM.json](https://github.com/staff0rd/docker-crankier/blob/master/client/vm/clientVM.json)
    *   一个 [ARM 模板](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates)，它指定了每个客户端要启动的虚拟机的结构。
*   [startUpScript.sh](https://github.com/staff0rd/docker-crankier/blob/master/client/vm/startUpScript.sh)
    *   一旦 docker 初始化并准备就绪，就在虚拟机上安装 docker。
*   [Up.ps1](https://github.com/staff0rd/docker-crankier/blob/master/client/vm/Up.ps1)
    *   异步提升大小为`vmSize`的`count`个虚拟机
*   运行命令. ps1
    *   在虚拟机上运行命令的速度并不快，因此这个脚本使用 ssh 和 powershell 作业实现了更快的命令运行速度。我们可以用它向所有虚拟机发送命令，并返回结果。

使用上面的脚本，我很快发现 Azure 默认每个区域设置了 20 个内核的限制。作为一种解决方法，我在每个区域增加了 10 个双核虚拟机。下面是一个启动 30 个虚拟机的示例:

```
.\Up.ps1  -count  10  -location  australiaeast  .\Up.ps1  -count  10  -offset  10  -location  westus  .\Up.ps1  -count  10  -offset  20  -location  eastus 
```

我可以用`Get-Job`和`Get-Job | Receive-Job`来监控启动虚拟机的进度。一旦工作完成，我就可以用`Get-Job | Remove-Job`清除它们。因为虚拟机都是异步启动的，所以全部启动总共需要大约 5 分钟。当它们启动后，我们可以向它们发送命令:

```
.\RunCommand.ps1  -command  "docker run --name crankier -d staff0rd/crankier --send-duration 10000 --target-url http://mypath/echo --connections 10000 --workers 20" 
```

如果我们已经正确设置了客户端的`target-url`，我们现在应该看到服务器回显传入的连接:

```
[00:00:00] Current: 178, peak: 178, connected: 160, disconnected: 0, rate: 160/s
[00:00:02] Current: 432, peak: 432, connected: 254, disconnected: 0, rate: 254/s
[00:00:02] Current: 801, peak: 801, connected: 369, disconnected: 0, rate: 369/s
[00:00:03] Current: 1171, peak: 1171, connected: 370, disconnected: 0, rate: 370/s
[00:00:05] Current: 1645, peak: 1645, connected: 474, disconnected: 0, rate: 474/s
[00:00:05] Current: 2207, peak: 2207, connected: 562, disconnected: 0, rate: 562/s
[00:00:06] Current: 2674, peak: 2674, connected: 467, disconnected: 0, rate: 467/s
[00:00:08] Current: 3145, peak: 3145, connected: 471, disconnected: 0, rate: 471/s
[00:00:08] Current: 3747, peak: 3747, connected: 602, disconnected: 0, rate: 602/s
[00:00:10] Current: 4450, peak: 4450, connected: 703, disconnected: 0, rate: 703/s 
```

## 监控客户端虚拟机连接

`RunCommand.ps`让我们向每个虚拟机发送任何命令，因此我们可以使用`docker logs`从每个虚拟机获取最后一行日志来监控它们的状态:

```
.\RunCommand.ps1  -command  "docker logs --tail 1 crankier" 
```

输出:

```
{"ConnectingCount":10,"ConnectedCount":8038,"DisconnectedCount":230,"ReconnectingCount":0,"FaultedCount":34,"TargetConnectionCount":10000,"PeakConnections":8038}

{"ConnectingCount":10,"ConnectedCount":8026,"DisconnectedCount":211,"ReconnectingCount":0,"FaultedCount":34,"TargetConnectionCount":10000,"PeakConnections":8026}

{"ConnectingCount":10,"ConnectedCount":7984,"DisconnectedCount":187,"ReconnectingCount":0,"FaultedCount":32,"TargetConnectionCount":10000,"PeakConnections":7986}
... 
```

这里有一个杀死容器的例子:

```
.\RunCommand.ps1  -command  "docker rm -f crankier" 
```

## 结果

在过去的三个月里，我创建了大约 980 个虚拟机，慢慢地改进了我测试和捕获数据的方式。下面的几行代表了其中的一些测试，后面的几行还包括测试的完整日志。

### 标准服务器 _D2s_v3

| 从开始算起的时间 | 峰值连接 | 日志 |
| --- | --- | --- |
| twenty five to four p.m. | Ninety-three thousand one hundred |  |
| 07:38 | One hundred thousand six hundred and sixty-nine |  |
| 24:16 | Ninety-one thousand five hundred and forty-one |  |
| 24:04 | Ninety-two thousand five hundred and six | [https://pastebin . com/qlgzt](https://pastebin.com/QPLgDeZt) |
| 07:54 | One hundred thousand seven hundred and thirty | [https://pastebin . com/FB 9 skzje](https://pastebin.com/FB9skzJE) |
| twenty nine to two p.m. | Ninety-one thousand five hundred and forty-one | [https://pastebin.com/sDLdm0bh](https://pastebin.com/sDLdm0bh) |

平均 80%的 CPU/RAM

### 标准 _D8s_v3 服务器

| 从开始算起的时间 | 峰值连接 | 日志 |
| --- | --- | --- |
| 02:34 | One hundred and seven thousand five hundred and sixty-four |  |
| 05:55 | One hundred and eleven thousand six hundred and sixty-five |  |
| 03:43 | One hundred and thirty-two thousand one hundred and seventy-five |  |
| 25:33 | Two hundred and ten thousand seven hundred and forty-six |  |
| three past one p.m. | Two hundred and fourteen thousand and twenty-five | [https://pastebin.com/wkttPAaS](https://pastebin.com/wkttPAaS) |

平均 40%的 CPU/RAM

### 标准 _D32s_v3 服务器

| 从开始算起的时间 | 峰值连接 | 日志 |
| --- | --- | --- |
| five past eleven | Two hundred and thirty-six thousand nine hundred and six | [https://pastebin.com/mm3RZM1y](https://pastebin.com/mm3RZM1y) |
| twenty eight past ten | Two hundred and forty-five thousand two hundred and seventeen | [https://pastebin.com/6kAPJB9R](https://pastebin.com/6kAPJB9R) |

平均 20%的 CPU/RAM

日志讲述了一个有趣的故事，包括每秒钟新连接的限制，以及 Kestrel 需要多长时间才会开始变慢:

> 在“05/21/2019 09:37:19 +00:00”时，心跳时间长于“00:00:01”

以及 SignalR 开始抛出以下异常的时间( [GitHub 问题](https://github.com/aspnet/AspNetCore/issues/6701) -可能的[修复](https://github.com/aspnet/AspNetCore/pull/10043))，

> 写入消息失败。中止连接。
> 
> 系统。InvalidOperationException:编写器完成后不允许写入

## 调查结果

我最初的目标是由 2018 年 4 月的这条推文引导的[，该推文建议 9.5GB 的 236k 并发连接数。从上面的测试来看，ASP.NET Core signaler 目前(`dotnet 3.0.100-preview6-011744`)似乎无法在如此低的内存下实现这样的数量。`B2ms`其峰值为 107k 有 8GB，与`D2s_v3`相似。然而，随着`D8s_v3`和`D32s_v3`分别达到 214k 和 245k 的峰值，很明显 CPU 和内存目前不是限制因素。使用我创建的工具，一次性自动部署服务器和客户端。NET Core 3 达到了 RTM](https://twitter.com/anurse/status/983406560880701440?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E983406560880701440%7Ctwgr%5E393039363b636f6e74726f6c&ref_url=http%3A%2F%2Fstaffordwilliams.com%2Fblog%2F2019%2F03%2F20%2Fazure-signalr-service%2F) 以后再测试就相对简单了。

## 更进一步

我在这个负载测试项目上投入了相当多的时间。这导致了[三个](https://hub.docker.com/r/staff0rd/pastebin) [新的](https://hub.docker.com/r/staff0rd/crankier) [容器](https://hub.docker.com/r/staff0rd/crankier-server)和几个[合并成 aspnet/aspnetcore](https://github.com/aspnet/AspNetCore/pulls?q=is%3Apr+author%3Astaff0rd+is%3Aclosed) 。即便如此，还是有事情要做。

分叉的 benchmarkserver 的功能应该被[移到 Crankier 本身](https://github.com/aspnet/AspNetCore/pull/9264#issuecomment-494565663)。服务器日志缺少重要的指标:总的 cpu &内存使用量，但是目前似乎没有一个好的方法来获取这些数据。NET Core(目前我在另一个到服务器的 ssh 会话中监控`top`)。最后，用户可以利用应用洞察，除了回显到 std out，还可以通过`TelemetryClient`将遥测推送到应用洞察——这将产生令人愉快的图形和对 pastebin 日志转储的日志查询。

## 最后一注

在熟悉了 Crankier 之后，我能够体会到它自身的局限性。当前的实现只测试并发连接，而不测试客户端和服务器之间的消息传递，这不能反映 SignalR 应用程序的“真实”负载。为了测试您自己的应用程序，不仅要测试消息传递，还要测试您的`Hub`实现所期望的消息传递。与其扩展 Crankier 来测试你自己的`Hub`方法，不如使用[微软要容易得多。AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) 编写您自己的类，该类将使用`HubConnection`直接调用您的应用程序的`Hub`方法，充当特定于您的应用程序的自动化用户。

以这种方式追踪并发连接数很有趣，但是不能反映产品应该是什么样子。增加虚拟机规模以实现更高的连接数会忽略一个虚拟机是应用程序的一个单点故障。在生产中，使用类似于 [Azure SignalR Service](https://staffordwilliams.com/blog/2019/03/20/azure-signalr-service/) 的东西将是扩展并发连接的更好方法。
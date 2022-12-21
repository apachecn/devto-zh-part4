# 自制物联网数据下载器

> 原文：<https://dev.to/aaronpowell/home-grown-iot-data-downloader-56nc>

我们上次讨论了如何构建物联网项目的数据结构，以及引导我最终选择该项目结构的一些决定，因此现在似乎是开始研究代码的好时机，特别是我用来从逆变器本身捕获数据的代码。

我将把重点放在下载器的代码上，它位于 GitHub repo 的这一部分[中，同时也涉及到我在这个项目中使用的一般开发方法。](https://github.com/aaronpowell/sunshine/blob/277c31fcb612d3866daf7759beb9525826778c2c/src/Sunshine.Downloader)

## 代码基础知识

让我们从代码库如何工作的一些基础开始。正如我在[序言](https://dev.to/azure/home-grown-iot-prologue-3k9h)中提到的，我选择用 F#来做这件事。NET 核心应用程序，这样我就可以轻松地将它部署到 Linux 和 Windows 上。我还使用 Docker 进行开发和部署(我将在以后的文章中介绍本地开发)，以使我更容易控制依赖项和环境。

我还选择使用 [Paket](https://fsprojects.github.io/Paket/) ，它是 NuGet 的替代包管理器，在 F#社区中非常常见，但也引入了锁文件的概念，以更好地处理传递依赖或冲突依赖版本。我承认我不喜欢 Paket，我确实有很多次不得不积极地与它斗争，而且它也不是一个. NET 核心工具，这意味着如果你想使用 Linux/WSL 或者依赖于[正在开发且不受支持的版本](https://github.com/fsprojects/Paket/issues/2875)，你就需要`mono`(这正是我所做的)。但是最后我把它全部修好了，所以我小心翼翼地不去碰它，以免弄坏东西！😝

代码库被分成 3 个项目，下载器(我们今天将讨论)、函数和模拟 API(用于本地开发)。这些都存在于`src`文件夹中，旁边是一些共享文件(在`Shared`中)。git repo 的根包含通常的 git 文件、我的用于构建管道的`azure-pipelines.yml`(我将在以后讨论管道)、Paket 文件和一个`.sln`。不可否认，我用 VS 代码做了大部分开发工作，但有时我会启动完整的 Visual Studio，尤其是在试图可视化依赖树的时候。

## 下载器

是时候开始研究下载器了，这样可以了解一下 ABB 变频器是如何公开其 API 的。但是有一件事我想从一开始就说清楚:

我正在处理一个没有文档记录的关于变频器的 API，并且以一种它不应该被使用的方式使用它。以这种方式访问您的逆变器应该不会损坏任何东西，但它可能会。我对你可能造成的任何损害不负任何责任！做这件事风险自担！

免责声明完成后，让我们看看如何获得数据。我的第一项工作是研究如何实际获取数据，毕竟，这不是一个文档化的 API，所以我需要研究存在哪些端点以及我需要访问什么。

我采取的方法是打开浏览器中的网络工具，同时在逆变器的仪表板上观看 XHR 网络事件。鉴于我已经确定它是一个 AngularJS 应用程序，它实际上只是一个观察网络流量以找到最有用的东西的问题。通过这样做，我发现了 4 个有趣的 API 端点(还有其他的，但是我不知道它们是做什么的，或者它们只是保持活动状态的测试):

*   `/v1/specs`
    *   这个 API 描述了可用的设备，并返回一个 JSON 有效载荷([示例](https://github.com/aaronpowell/sunshine/blob/277c31fcb612d3866daf7759beb9525826778c2c/src/Shared/sample-data/specs.json))，该载荷为我提供了关于`device`(逆变器)和`logger`(向 ABB 的云平台发送数据的东西)的信息
    *   最终，这个 API 是一个元数据端点，我不需要*调用它，但是我需要调用它，因为这意味着我不需要在解决方案中对我的设备进行任何硬编码*
    *   当 AngularJS 应用程序第一次启动时，仪表板似乎会调用这个函数，以后再也不会调用了(我猜值在 JavaScript 内存中)
*   `/v1/livedata/list`
    *   这是`livedata`下的两个 API 之一，也是另一个元数据端点，这次它给我提供了关于逆变器正在监控哪些传感器的信息，我可以从中获取数据。它还提供了关于它们的信息，如它们的度量单位、描述(一般来说，实际上不是描述性的！)和小数精度。我在 GitHub 上又有了一个例子
    *   仪表板似乎每 5 分钟调用一次。我不知道为什么它会刷新这个，希望传感器不会经常改变！
*   `/v1/livedata`
    *   这是有趣的部分，这是数据有用的地方，它返回由`livedata/list` ( [示例](https://github.com/aaronpowell/sunshine/blob/277c31fcb612d3866daf7759beb9525826778c2c/src/Shared/sample-data/live-data.json))描述的传感器的值
    *   仪表板调用这个**很多次**，大约每 30 秒一次，这是合理的，因为它是主要的数据馈送
*   `/v1/feeds`
    *   这个 API 让我很困惑，因为它总是被一堆查询字符串值调用，这些值在 5 分钟内返回`Pgrid`。通过查看数据，*看起来*认为`Pgrid`与从逆变器到电网的功率(瓦特)有关(从名称上看这是合理的),但是如果我想获得其他指标，我无法在查询字符串中找到任何其他可以调整的内容。我想我也在`livedata`得到了这个信息，但是捕获两次也无妨，毕竟，他们为此专门开发了一个 API。无论如何，这个响应的结构很奇怪([例子](https://github.com/aaronpowell/sunshine/blob/277c31fcb612d3866daf7759beb9525826778c2c/src/Shared/sample-data/pgrid-sum.json))但是并不*太难理解*
    *   仪表板似乎大约每 5 分钟调用一次，如果响应是 5 分钟时间片，就没有必要比这更频繁地调用它

有了我们要调用的 4 个 API，我们就可以开始构建我们的应用程序了。

### API 助手

我正在访问的 API 是使用基本身份验证来保护的，它向包含 base 64 编码字符串`username:password`的请求添加了一个`Authorization`令牌，不是非常安全，但也可以。我们将创建一个小的帮助函数来为我们生成它:

```
let getAuthToken username password =
    sprintf "%s:%s" username password
    |> ASCIIEncoding.ASCII.GetBytes
    |> Convert.ToBase64String 
```

Enter fullscreen mode Exit fullscreen mode

为了访问 API，我使用了 [FSharp。数据](http://fsharp.github.io/FSharp.Data/)的 [HTTP 实用程序](http://fsharp.github.io/FSharp.Data/library/Http.html)，并将其包装在一个名为`getData` :
的函数中

```
let getData authToken baseUri (path : string) =
    let url = Uri(baseUri, path)
    printfn "Requesting: %s" (url.ToString())
    Http.AsyncRequestString
        ( url.ToString(),
          httpMethod = "GET",
          headers = [ Accept HttpContentTypes.Json
                      Authorization (sprintf "Basic %s" authToken) ] ) 
```

Enter fullscreen mode Exit fullscreen mode

该函数获取令牌(从`getAuthToken`输出)和变频器的 API 库，我们向其中添加特定的 API 路径。然后，这个小函数将设置封装在适当的头中，并发出请求以获取 JSON 响应供我们使用。

### 启动应用程序

当下载器启动时，它打印出[标志](https://github.com/aaronpowell/sunshine/blob/277c31fcb612d3866daf7759beb9525826778c2c/src/Sunshine.Downloader/Logo.fs)(帮助我在日志中发现它何时重启🤣)然后建立到 Azure IoT Hub 的连接。

### 建立物联网客户端连接

有了这个解决方案，我要么在本地运行它，要么在我的 Raspberry Pi 上运行它，我调整我建立连接的方式，更重要的是，调整连接的类型。对于本地开发，我连接到物联网中心作为一个[管理设备](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-device-management-overview?WT.mc_id=devto-blog-aapowell)，但当它在树莓 Pi 上时，它使用物联网边缘作为一个[模块](https://docs.microsoft.com/en-us/azure/iot-edge/iot-edge-modules?WT.mc_id=aaronpowell-blog-aapowell)进行部署。当我进行本地开发和部署时，我将更详细地讨论这两个主题，但是要知道，除了一个使用 [`DeviceClient`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.devices.client.deviceclient?view=azure-dotnet&WT.mc_id=devto-blog-aapowell) 和另一个使用 [`ModuleClient`](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet&WT.mc_id=devto-blog-aapowell) 之外，您的连接方式并没有太大的不同。

正因为如此，我用一个 F#记录类型和利用函数作为引用来包装客户端(除了`Object`，它们不共享一个公共的基本类型:

```
module IoTWrapper
open Microsoft.Azure.Devices.Client
open Microsoft.Azure.Devices.Shared

type IoTConnectionWrapper =
  { SendEventAsync : Message -> Async<unit>
    GetTwinAsync : unit -> Async<Twin> }

let getIoTHubClient iotConnStr =
    async {
    return! match iotConnStr with
            | null | "" ->
              async {
              let amqpSetting = AmqpTransportSettings(TransportType.Amqp_Tcp_Only) :> ITransportSettings;
              let! client = [| amqpSetting |]
                            |> ModuleClient.CreateFromEnvironmentAsync
                            |> Async.AwaitTask
              do! client.OpenAsync() |> Async.AwaitTask
              return { SendEventAsync = fun msg -> client.SendEventAsync msg |> Async.AwaitTask
                       GetTwinAsync = fun () -> client.GetTwinAsync() |> Async.AwaitTask } }
            | _ ->
              async {
              let client = DeviceClient.CreateFromConnectionString iotConnStr
              return { SendEventAsync = fun msg -> client.SendEventAsync msg |> Async.AwaitTask
                       GetTwinAsync = fun () -> client.GetTwinAsync() |> Async.AwaitTask } } } 
```

Enter fullscreen mode Exit fullscreen mode

`getIoTHubClient`获取一个连接字符串(为本地开发传递的)并使用模式匹配来检查我是否提供了一个连接字符串(隐含的本地开发)。*边注:我爱 F#图案搭配！*

使用`| null | "" ->`的模式允许我测试一些不同的结果，并让它们都产生在同一个块中，而`| _ ->`的意思是“以前没有匹配的任何东西”。我还广泛使用了 [F#异步工作流](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/asynchronous-workflows?WT.mc_id=aaronpowell-blog-aapowell)来展开 C# `Task` API，使其更好地适应 F#的异步方法。

### 使用物联网双胞胎

当涉及到使用物联网解决方案时，设备上运行的代码可能非常通用，毕竟，它可能被部署到数百台设备上，所以你不希望将秘密嵌入到代码库中，因为这样你在任何地方都有相同的秘密。虽然 **I** 可能只有一台设备，但我仍然不想在代码中嵌入我的逆变器的用户名、密码和 IP 地址(我不想让 GitHub 上的变成**！)所以我需要某种方法以安全的方式将它们下载到设备上。**

最初，当 Docker 容器开始传递环境变量时，我沿着在 Docker 容器上设置环境变量的道路前进，但是当涉及到部署到物联网设备时，这就有点棘手了，相反，我决定使用[设备孪生](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-device-twins?WT.mc_id=devto-blog-aapowell)(附带说明:如果您使用的是`ModuleClient`，您将使用模块孪生，它与设备孪生相同，但范围仅限于该模块)。Device Twin 是设备的 JSON 配置文件，存储在 Azure 中，可以由设备或通过 portal/ `az` cli(以及任何第三方工具)进行更新。

在我的 Twin 中，我添加了一个新的带有 API 认证信息的`Desired Property`:

```
 //  snip  "properties":  {  "desired":  {  "inverter":  {  "username":  "...",  "password":  "...",  "url":  "http://..."  },  "$metadata":  {  //  and  so  on  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

然后就是[得到双胞胎信息](https://github.com/aaronpowell/sunshine/blob/277c31fcb612d3866daf7759beb9525826778c2c/src/Sunshine.Downloader/Program.fs#L21-L23) :
的问题了

```
 async {
    let! iotClient = getIoTHubClient iotConnStr

    let! twin = iotClient.GetTwinAsync()

    let iotProperties = parseDesiredProperties twin.Properties.Desired 
```

Enter fullscreen mode Exit fullscreen mode

`parseDesiredProperties`是一个[的小函数](https://github.com/aaronpowell/sunshine/blob/277c31fcb612d3866daf7759beb9525826778c2c/src/Sunshine.Downloader/IoTWrapper.fs#L28-L32)，帮助将返回的`TwinCollection`解包为对我有用的类型:

```
open FSharp.Data
type DesiredProperties = JsonProvider<""" {"inverter":{"username":"user","password":"pass","url":"http://localhost/"},"$version":4} """>

let parseDesiredProperties (data : TwinCollection) =
  data.ToJson() |> DesiredProperties.Parse 
```

Enter fullscreen mode Exit fullscreen mode

您可能注意到我使用了 FSharp 的 [JSON 类型提供者](http://fsharp.github.io/FSharp.Data/library/JsonProvider.html)。数据，我经常使用它将 JSON 表示转换回强类型对象。

现在我可以做一些局部应用来设置`getData`函数，以便在应用:
中多次使用

```
let token = getAuthToken iotProperties.Inverter.Username iotProperties.Inverter.Password
let getData' = getData token (Uri iotProperties.Inverter.Url) 
```

Enter fullscreen mode Exit fullscreen mode

这意味着每当我需要进行 API 调用时，我可以使用已经应用了访问令牌和基于 URI 参数的转换器的`getData'`。

### 轮询 API

另一个相对复杂的部分是在不同的时间表轮询这些 API。我可以使用类似 [Hangfire](https://www.hangfire.io/) 的东西，但是我还需要包含一个 SQL Server(或类似的)来有效地管理轮询，这将增加成本和复杂性。如果我为多台设备这样做，我会推荐它，但对于主要供个人使用的单台设备，停机和数据丢失并不是世界末日。

相反，我使用 F#异步工作流中的`Async.Start`来生成一个新线程，然后运行一个递归函数，[就像这样的](https://github.com/aaronpowell/sunshine/blob/277c31fcb612d3866daf7759beb9525826778c2c/src/Sunshine.Downloader/Program.fs#L49-L57) :

```
let rec dataPoller() = async {
    match! getLiveData getData' deviceId with
    | Some liveData -> do! sendIoTMessage iotClient "liveData" correlationId liveData
    | None -> ignore()

    do! int(TimeSpan.FromSeconds(20.).TotalMilliseconds) |> Async.Sleep
    dataPoller() |> Async.Start }

dataPoller() |> Async.Start 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我每 20 秒调用一次`livedata` API(这样有延迟，等等。我大约每 30 秒得到 1)，对我拥有的 [API 包装器](https://github.com/aaronpowell/sunshine/blob/277c31fcb612d3866daf7759beb9525826778c2c/src/Sunshine.Downloader/LiveData.fs#L25-L39)使用模式匹配，它返回一个 [`Option`](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/options?WT.mc_id=devto-blog-aapowell) 用于错误处理，并假设它成功地将数据发送到物联网中心。为什么我要用递归函数呢？好吧，它只是一个递归函数，事实上，函数调用它自己，它实际上不传递任何数据到新的调用中，我们可以像某种循环那样做，但像这样的递归函数更像是一个常见的 F#模式。你可能想知道堆栈溢出异常，幸好 F#在递归函数方面有一些很好的优化，所以我不应该碰到它，我已经让它运行了几天，产生了成千上万的消息，它没有崩溃！ *…然而*

### 将 API 调用链接在一起

每个 API 调用都发生在单独的后台作业中，由单独的递归函数处理

### 保持应用程序运行

我需要应用程序做的最后一件事是不退出，因为它不知道要等到后台作业停止。最初，我打算依赖于`Console.ReadLine()`,并让它等待直到一个换行符被发送，但是如果它运行在一个没有附加`stdin`的 Docker 容器中(即:一个非交互式容器),这是它被部署到 Raspberry Pi 的方式，这就不起作用了。

方便的是，我们有一种方法可以测试它， [`Console.IsInputRedirected`](https://docs.microsoft.com/en-us/dotnet/api/system.console.isinputredirected?view=netcore-2.2?WT.mc_id=aaronpowell-blog-aapowell) ，我们可以这样组合:

```
printfn "Background jobs running, now we're waiting... "
if Console.IsInputRedirected then
    while true do
        do! Async.Sleep 300000
else
    Console.ReadLine() |> ignore 
```

Enter fullscreen mode Exit fullscreen mode

现在，根据我们的容器如何启动，我们要么等待换行符终止，要么应用程序将无限期运行。

## 结论

下载器的完整代码库在 GitHub 上有[(在撰写本文时，我已经将这篇文章提交到了`HEAD`)。]((https://github.com/aaronpowell/sunshine/blob/277c31fcb612d3866daf7759beb9525826778c2c/src/Sunshine.Downloader))

在这篇文章中，我们看到了如何利用部分应用程序和模式匹配等一些 F#语言特性来实现我们的一些目标，以及如何向设备提供证书/机密，而无需将它们嵌入到应用程序中。

我希望这能让你对我从逆变器中收集数据的方法有所了解。我很喜欢这种方法的反馈，有没有什么可以做得更简单的？没道理？似乎工程过度？
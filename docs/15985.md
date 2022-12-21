# 国产物联网处理数据

> 原文：<https://dev.to/azure/home-grown-iot-processing-data-5cnm>

[上一次](https://dev.to/azure/home-grown-iot-local-dev-246b)我们看了如何从物联网设备获取数据，并开始将其推送到 Azure，现在是下一步的时候了，在数据到来时对其进行处理。

我在[解决方案设计](https://dev.to/azure/home-grown-iot-solution-design-io5)中提到，数据的处理将通过 [Azure 函数](https://docs.microsoft.com/en-us/azure/azure-functions/?WT.mc_id=devto-blog-aapowell)来完成，所以让我们来看看它是如何工作的。

## 用函数处理数据

Azure Functions 内置了对从[物联网中心](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-event-iot?WT.mc_id=devto-blog-aapowell)处理数据的支持，这使得集成变得非常容易。唯一的缺点是，它监控物联网中心提供的内置事件中心，如果您有多个数据结构被提交(像我一样)，您的功能将变得复杂。相反，我将使用[事件中心绑定](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-event-hubs?WT.mc_id=devto-blog-aapowell)。

## 设计功能

当设计功能时，或者一般来说是无服务器的，你想让它们尽可能的小；我们的目标不是创造一个无服务器的庞然大物！这意味着设计的一部分需要你考虑你的函数将扮演什么角色。对我来说，他们将负责将从物联网设备发送的 JSON 有效载荷转换为存储在表存储中的结构。如果我们考虑我在[数据下载者帖子](https://dev.to/aaronpowell/home-grown-iot-data-downloader-56nc)中描述的 API，有一个端点`livedata`，它为我提供了捕获所需的大部分数据。

在对真正的 API 进行了一些检查之后，我注意到有 3 个桶可以表示这些数据:

*   固定样本数据
*   时间点摘要数据
*   杂项数据

我决定将这些作为单独的表存储在表存储中(要了解更多，请查看关于[数据设计](https://dev.to/azure/home-grown-iot-data-g18)的帖子)。因为它们都来自同一个原始结构，所以我*可以*在一个函数中完成所有的工作，但是我把它分成了 3 个函数，尽可能地保持每个函数的轻量级。

## 编写我们的函数

这些函数是用 F#实现的([下面是如何设置的](https://dev.to/azure/azure-functions-with-f-2l3c))，我使用的是 [FSharp。Azure.Storage](https://github.com/fsprojects/FSharp.Azure.Storage) NuGet package 使使用 Table Storage SDK 更加 F#友好。

*注意:如果你打算在 F# Azure 函数中使用 NuGet 包，你需要非常小心你所依赖的版本。由于函数在内部使用表存储，因此有可能带来冲突版本，从而导致错误。我通过在我的`paket.dependencies`文件中非常[明确地锁定](https://github.com/aaronpowell/sunshine/blob/c1005c8bf8ec1d295f05398556bd1bf8dccd7e36/paket.dependencies#L11-L12)解决了这个问题。*

我们将从定义存储在表存储中的记录类型开始:

```
type PanelInfo =
     { [<PartitionKey>] DateStamp: string
       [<RowKey>] Id: string
       Panel: string
       MessageId: string
       Current: float // Iin#
       Volts: float // Vin#
       Watts: float // Pin#
       MessageTimestamp: DateTime // SysTime
       CorrelationId: string } 
```

在记录类型上，我添加了属性来表示哪些成员将成为表存储中的分区和行键，这使得我在需要时可以更好地处理对象模型。这种类型用于表示我的太阳能设置中的单组面板的数据，并为我提供跨伏特、瓦特和电流的入站值视图。

为了定义函数本身，我们在模块中创建一个成员:

```
[<FunctionName("PanelTrigger")>]
let trigger
    ([<EventHubTrigger("live-data", ConsumerGroup = "Panel", Connection = "IoTHubConnectionString")>] eventData: EventData)
    ([<Table("PanelData", Connection = "DataStorageConnectionString")>] panelDataTable: CloudTable)
    (logger: ILogger) =
        ignore() // todo 
```

我们将成员(我称之为`trigger`)属性化为`FunctionName`，这样函数主机就知道如何找到它，并知道给它取什么名字。不幸的是，您需要显式地声明类型，F#不能根据绑定中复杂类型的使用推断类型(至少，在我的经验中不能)。

这个函数有 3 个输入，第一个是事件中心绑定，它使用消费者组`Panel`绑定到一个名为`live-data`的事件中心(参见[解决方案设计](https://dev.to/azure/home-grown-iot-solution-design-io5)部分**多次处理消息**了解我为什么使用消费者组)。我们还将使用`EventData`类型作为输入，而不是`string`，这样我们就可以访问消息的元数据，而不仅仅是正文(当类型为`string`时，正文就会出现)。接下来是绑定到表存储的输出，绑定为一个`CloudTable`，它为我提供了带有`FSharp.Storage.Data`的互操作。最后是`ILogger`，这样我可以从函数中注销消息。

### 解包消息

是时候开始处理消息了，为此我需要提取消息体(并用类型提供者对其进行强类型化并获取一些元数据:

```
[<FunctionName("PanelTrigger")>]
let trigger
    ([<EventHubTrigger("live-data", ConsumerGroup = "Panel", Connection = "IoTHubConnectionString")>] eventData: EventData)
    ([<Table("PanelData", Connection = "DataStorageConnectionString")>] panelDataTable: CloudTable)
    (logger: ILogger) =
    async {
        let message = Encoding.UTF8.GetString eventData.Body.Array
        let correlationId = eventData.Properties.["correlationId"].ToString()
        let messageId = eventData.Properties.["messageId"].ToString()

        let parsedData = LiveDataDevice.Parse message 
```

`EventData`对象让我们以 [ArraySegment](https://docs.microsoft.com/en-us/dotnet/api/system.arraysegment-1?view=netframework-4.8&WT.mc_id=devto-blog-aapowell) 的形式访问消息体，但是我们想要整个数组，它由`Array`属性公开。这是一个 UTF8 编码的字节数组，所以我们必须把它解码成 JSON 的字符串(或者任何你的传输结构)。然后，因为我们可以访问整个消息，而不仅仅是消息体，所以我们可以访问下载器放入消息的附加属性，即`CorrelationId`和`MessageId`。

因为数据点以键/值对数组的形式出现，所以我创建了一个函数来查找特定点的值:

```
let findPoint (points: LiveDataDevice.Point[]) name =
    let point = points |> Array.find(fun p -> p.Name = name)
    float point.Value 
```

然后使用 partial application 将解析的数据绑定到它:

```
[<FunctionName("PanelTrigger")>]
let trigger
    ([<EventHubTrigger("live-data", ConsumerGroup = "Panel", Connection = "IoTHubConnectionString")>] eventData: EventData)
    ([<Table("PanelData", Connection = "DataStorageConnectionString")>] panelDataTable: CloudTable)
    (logger: ILogger) =
    async {
        let message = Encoding.UTF8.GetString eventData.Body.Array
        let correlationId = eventData.Properties.["correlationId"].ToString()
        let messageId = eventData.Properties.["messageId"].ToString()

        let parsedData = LiveDataDevice.Parse message
        let findPoint' = findPoint parsedData.Points 
```

### 写入存储

因为我需要将 2 个面板组写入存储，所以我在函数中创建了一个函数来完成这项工作:

```
[<FunctionName("PanelTrigger")>]
let trigger
    ([<EventHubTrigger("live-data", ConsumerGroup = "Panel", Connection = "IoTHubConnectionString")>] eventData: EventData)
    ([<Table("PanelData", Connection = "DataStorageConnectionString")>] panelDataTable: CloudTable)
    (logger: ILogger) =
    async {
        let message = Encoding.UTF8.GetString eventData.Body.Array
        let correlationId = eventData.Properties.["correlationId"].ToString()
        let messageId = eventData.Properties.["messageId"].ToString()

        let parsedData = LiveDataDevice.Parse message
        let findPoint' = findPoint parsedData.Points
        let deviceId = parsedData.DeviceId.ToString()
        let timestamp = epoch.AddSeconds(findPoint' "SysTime")

        let storePanel p =
               let panel =
                   { DateStamp = timestamp.ToString("yyyy-MM-dd")
                     Panel = p
                     Id = Guid.NewGuid().ToString()
                     MessageId = messageId
                     Current = findPoint' (sprintf "Iin%s" p)
                     Volts = findPoint' (sprintf "Vin%s" p)
                     Watts = findPoint' (sprintf "Pin%s" p)
                     MessageTimestamp = timestamp
                     CorrelationId = correlationId }
               panel |> Insert |> inTableToClientAsync panelDataTable 
```

这使用传入的面板号(`let! _ = storePanel "1"`就是这样调用的)创建了记录类型，然后将它从我的外部库传递给`Insert`函数。但是`FSharp.Azure.Storage`库被设计为与来自 SDK 的客户端一起工作，并将其转换为`CloudTable`，它并没有 100%优化用于 Azure 函数，尽管这是一个简单的修复，这里有一个函数来处理这个问题:

```
let inTableToClientAsync (table: CloudTable) o = inTableAsync table.ServiceClient table.Name o 
```

最后，因为我们使用 F#的异步工作流，而 Azure 函数主机只处理`Task<T>` (C#的异步)，我们需要用`Async.StartAsTask`将其转换回来:

```
[<FunctionName("PanelTrigger")>]
let trigger
    ([<EventHubTrigger("live-data", ConsumerGroup = "Panel", Connection = "IoTHubConnectionString")>] eventData: EventData)
    ([<Table("PanelData", Connection = "DataStorageConnectionString")>] panelDataTable: CloudTable)
    (logger: ILogger) =
    async {
        let message = Encoding.UTF8.GetString eventData.Body.Array
        let correlationId = eventData.Properties.["correlationId"].ToString()
        let messageId = eventData.Properties.["messageId"].ToString()

        let parsedData = LiveDataDevice.Parse message
        let findPoint' = findPoint parsedData.Points
        let deviceId = parsedData.DeviceId.ToString()
        let timestamp = epoch.AddSeconds(findPoint' "SysTime")

        let storePanel p =
               let panel =
                   { DateStamp = timestamp.ToString("yyyy-MM-dd")
                     Panel = p
                     Id = Guid.NewGuid().ToString()
                     MessageId = messageId
                     Current = findPoint' (sprintf "Iin%s" p)
                     Volts = findPoint' (sprintf "Vin%s" p)
                     Watts = findPoint' (sprintf "Pin%s" p)
                     MessageTimestamp = timestamp
                     CorrelationId = correlationId }
               panel |> Insert |> inTableToClientAsync panelDataTable

        let! _ = storePanel "1"
        let! _ = storePanel "2"

        logger.LogInformation(sprintf "%s: Stored panel %s for device %s" correlationId messageId deviceId)
    } |> Async.StartAsTask 
```

这样，我们收到的每条消息都有 2 个条目被写入表存储。

## 结论

我不会详细介绍项目中的每个功能，因为它们都遵循相同的模式，相反，你可以在 GitHub 上找到它们[。](https://github.com/aaronpowell/sunshine/tree/c1005c8bf8ec1d295f05398556bd1bf8dccd7e36/src/Sunshine.Functions)

虽然这可能感觉有点像对代码库的微观管理，拥有一大堆不到 50 行的函数，但这使它们在迭代开发时更容易维护和编辑。它还使它的伸缩性非常好，我发现有几次当我不小心禁用了 24 小时的功能，并且在 Event Hub 中有大量的积压要处理时，它可以快速完成任务！

我希望这能让你对如何创建自己的 Azure 函数栈来处理数据有所了解，无论是来自物联网设备还是其他输入流。
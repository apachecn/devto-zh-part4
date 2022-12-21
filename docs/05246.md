# 在 Azure Functions V2 中使用触发器和绑定

> 原文：<https://dev.to/rickvdbosch/using-triggers-bindings-in-azure-functions-v2-1nk1>

首先:是的。关于 Azure 函数中所有可用的触发器和绑定，有一些相当不错的文档。像这样的[总览页面](https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings)。您还可以找到关于特定绑定的文档，比如[服务总线绑定](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-service-bus)。尽管如此，我还是想在文档中添加一些使用触发器&绑定的简单、真实的例子。所以现在开始。

## 写入 Blob 存储器

考虑这个非常简单的 [BlobTrigger](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-storage-blob#trigger) ed Azure 函数。它将触发文件复制到 Azure Blob 存储中的不同容器:

```
public static async Task Run(
    [BlobTrigger("upload/{name}", Connection = "scs")]Stream addedBlob, 
    string name, ILogger log)
{
    // Setting up my BlobStorageRepository (it's a wrapper)
    var connectionString = Environment.GetEnvironmentVariable("scs", EnvironmentVariableTarget.Process);
    var blobStorageRepository = new BlobStorageRepository(connectionString);

    await blobStorageRepository.AddFileAsync("copied", name, addedBlob);
} 
```

Enter fullscreen mode Exit fullscreen mode

这看起来是个不错的解决方案。但是绑定可以使代码变得简单得多！

考虑下一个代码:

```
public static async Task Run(
    [BlobTrigger("upload/{name}", Connection = "scs")]Stream addedBlob,
    [Blob("copied/{name}", FileAccess.Write, Connection = "scs")]Stream stream,
    string name, ILogger log)
 {
    await addedBlob.CopyToAsync(stream);
 } 
```

Enter fullscreen mode Exit fullscreen mode

这里的结果是一样的！通过使用 out 绑定，您可以直接访问“复制”容器中的流。您可以使用输入绑定中的“name”参数。

其次，你可以绑定的不仅仅是流。对于外部绑定，下面是您可以绑定到的类型列表(摘自[本文](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-storage-blob#output---usage)):

*   文本作者
*   输出字符串
*   输出字节[]
*   云团流
*   溪流
*   CloudBlobContainer
*   cloud blob 目录
*   ICloudBlob
*   CloudBlockBlob
*   CloudPageBlob
*   CloudAppendBlob

这意味着您还可以绑定到例如 CloudBlobContainer 来输出多个 blobs。

将消息放到服务总线上
说到 Blobs，您可能不想基于一个触发器编写大量的消息。虽然我能想到一些场景...

然而对于服务总线来说，这种场景的可能性要大得多。让我们看看如何使用 ServiceBusBinding 来实现它。下面的函数读取一个文件。然后，它在一个空格上拆分内容，并将每个单词作为一个单独的消息放入服务总线队列:

```
public static void Run(
    [BlobTrigger("to-process/{name}", Connection = "scs")]Stream addedBlob,
    [ServiceBus("process", Connection = "sbcss", EntityType = EntityType.Queue)] ICollector queueCollector,
    string name, ILogger log)
{
    using (var reader = new StreamReader(addedBlob))
    {
        var words = (await reader.ReadToEndAsync()).Split(' ');
        Parallel.ForEach(words.Distinct(), (word) =>     
        {
            queueCollector.Add(word);     
        }); 
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在感觉就像魔术一样了？几乎所有的事情都为您抽象了:连接和编写服务总线甚至不再是您关心的事情。
对于服务总线输出绑定，可以绑定的类型有(取自[这里的](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-service-bus#output---usage)):

*   out T 参数名
*   输出字符串
*   输出字节[]
*   外出消息
*   ICollector 或 IAsyncCollector

ICollector 和 IAsyncCollector 用于创建多条消息。调用 Add 方法时会创建一条消息。在异步函数中，使用返回值或 IAsyncCollector，而不是 out 参数。

IAsyncCollector。AddAsync()行为取决于您使用的绑定类型>以及底层服务是否支持批处理。如果服务需要，调用 AddAsync 只会准备项目。在这种情况下，调用 FlushAsync()实际上会刷新它们。当函数成功完成时，将自动调用 FlushAsync()。

当前支持批处理的服务:

*   SendGrid
*   Twilio
*   EventHub

GitHub 上有一个悬而未决的问题，即他们需要推动跨绑定的 IAsyncCollector 批处理的一致性。一些需要关注的事情。

希望这有所帮助。

*本文原载于[rickvandenbosch.net](https://www.rickvandenbosch.net/blog/using-triggers-bindings-in-azure-functions-v2/)T3】*
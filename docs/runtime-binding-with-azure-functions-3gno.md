# Azure 函数的运行时绑定

> 原文：<https://dev.to/azure/runtime-binding-with-azure-functions-3gno>

> 本文是 [#ServerlessSeptember](https://dev.to/azure/serverless-september-content-collection-2fhb) 的一部分。在这个无服务器的内容集合中，您可以找到其他有用的文章、详细的教程和视频。9 月份，每天都有来自社区成员和云倡导者的新文章发布，没错，每天都有。
> 
> 在[https://docs.microsoft.com/azure/azure-functions/](https://docs.microsoft.com/azure/azure-functions/?WT.mc_id=servsept_devto-blog-cxa)了解更多关于微软 Azure 如何实现你的无服务器功能。

无服务器功能很少单独工作。功能通常与其他服务配对，如数据库、存储服务，甚至可能是电子邮件通知服务。 [Azure Functions](https://azure.microsoft.com/services/functions/) 通过提供[绑定](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings)让你与一系列[云服务](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings#supported-bindings)的现成连接，让你轻松连接这些服务。您可以使用标准配置实现很多目标，但是如果您需要超越默认配置呢？

假设您想要:

*   根据传入的 IP 地址更改存储位置？
*   基于开发和生产环境切换电子邮件收件人？
*   基于日期范围将数据写入不同的消息队列？

这些只是使用绑定的默认设置可能难以进行的调整类型的几个示例。本文演示了如何在运行时配置 C# Azure Functions 绑定，以完全控制绑定的配置。

## TL；博士；医生

要在运行时配置绑定:

1.  将绑定参数声明为一个 [Binder 或 IBinder](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-class-library#binding-at-runtime) 实例。
2.  创建绑定属性的实例。
3.  根据需要自定义绑定属性。
4.  使用`Bind`或`BindAsync`将属性绑定到云服务。
5.  使用绑定

GitHub 上有一个工作示例:[azure-functions-runtime-binding](https://github.com/craigshoemaker/azure-functions-runtime-binding)。

## 默认绑定

在深入研究运行时绑定如何工作的细节之前，首先考虑默认场景。下面的代码示例实现了一个 HTTP 触发的函数，该函数将消息保存到一个 [Azure 存储](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob) blob 容器中。

```
[FunctionName("SaveDefault")]
public static IActionResult Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]
    HttpRequest req,

    [Blob("messages/{sys.randguid}.txt", FileAccess.Write)] out string blob,
    ILogger log)
{
    string message = req.Query["message"];
    blob = $"Default binding: {message}";

    return new OkResult();
} 
```

Enter fullscreen mode Exit fullscreen mode

为了使绑定在这种情况下工作，`blob`参数以下列方式设置:

*   属性被声明为写入映射到名为`messages`的 blob 容器的数据。
*   替换令牌确保新的 blob 被赋予唯一的名称。
*   该参数被声明为`string`。
*   该参数被声明为`out`参数。使用 out 参数使绑定有机会获取原语`string`的值，并使用它写出到绑定逻辑中其他位置的存储容器。

这种设置非常适合简单的场景。但是，使用这种方法很难对绑定进行更改。在某些情况下，您可能希望:

*   更改连接字符串。
*   计算 blob 名称和/或容器名称。
    *   您确实有一些替换令牌和路由参数可用来影响 blob 路径，但是有些需求更复杂。
*   动态更改文件访问类型。也许在某些情况下，您希望创建具有`Write`或`ReadWrite`访问级别的属性。

为了实现更灵活的绑定定制，您可以实现运行时绑定。

## 运行时绑定

以下函数的最终结果与默认场景中实现的结果相同。这个实例的不同之处在于，绑定是强制创建的，这使您有机会影响绑定的行为。

```
[FunctionName("SaveCustom")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]
    HttpRequest req,
    IBinder binder,
    ILogger log)
{
    string message = req.Query["message"];

    var attribute = new BlobAttribute("messages/{sys.randguid}.txt", FileAccess.Write);
    attribute.Connection = "AzureStorageConnectionString";

    using(var blob = await binder.BindAsync<TextWriter>(attribute))
    {
        blob.Write($"Runtime binding: {message}");
    }

    return new OkResult();
} 
```

Enter fullscreen mode Exit fullscreen mode

绑定被声明为一个`IBinder`类型，这使您有机会决定绑定配置。

在这种情况下:

*   如默认示例所示，通过向构造函数传递相同的参数来创建`BlobAttribute`的实例。
    *   在这里，您可以决定使用自定义逻辑来确定 blob 容器名称或生成自定义 blob 名称。
*   设置了`Connection`属性。
    *   在这里，你可以根据环境或者函数的输入来替换应用程序的设置名称。
*   blob 绑定是通过调用`binder.BindAsync`创建的，因为`TextWriter`类用于写入 blob 容器。
*   调用`blob.Write`最终将数据保存到 blob 容器中。

> **注意:**不是将绑定键入为`string`(如默认场景所示)，而是将绑定创建为`TextWriter`。使用`TextWriter`类是因为`string`实例不知道如何写回 blob 容器。`TextWriter`配置为通过`binder.BindAsync`的调用了解 blob 容器。

## 代码示例

GitHub 上有样例代码:[azure-functions-runtime-binding](https://github.com/craigshoemaker/azure-functions-runtime-binding)。

## 总结

内置的 Azure 函数绑定让你可以丰富地访问许多不同的云服务。虽然默认行为在许多情况下可能工作得很好，但有时您需要定制绑定的配置。

您可以通过以下步骤在运行时自定义绑定:

1.  将绑定参数声明为一个 [Binder 或 IBinder](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-class-library#binding-at-runtime) 实例。
2.  创建绑定属性的实例。
3.  根据需要自定义绑定属性。
4.  使用`Bind`或`BindAsync`将属性绑定到云服务。
5.  使用绑定
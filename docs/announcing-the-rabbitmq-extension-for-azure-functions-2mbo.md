# 宣布 Azure 函数的 RabbitMQ 扩展

> 原文：<https://dev.to/azure/announcing-the-rabbitmq-extension-for-azure-functions-2mbo>

我们今天很兴奋地宣布 Azure Functions 运行时的 RabbitMQ 扩展的公开预览。有了这个新的扩展，您现在可以让函数触发对队列消息的响应，或者连接到 RabbitMQ 进行输出绑定。目前，当将功能发布到启用运行时驱动规模的[功能高级计划](https://docs.microsoft.com/azure/azure-functions/functions-premium-plan)或专用的[应用服务计划](https://docs.microsoft.com/en-us/azure/azure-functions/functions-scale#app-service-plan)时，支持预览触发器。你也可以和 KEDA 一起使用 RabbitMQ 和 [Kubernetes 中的 Azure Functions 容器。](https://docs.microsoft.com/en-us/azure/azure-functions/functions-kubernetes-keda#deploying-a-function-app-to-kubernetes)

## 如何开始使用 RabbitMQ 触发器

要开始使用 RabbitMQ 触发器，您需要在您的函数项目中包含扩展。

#### [T1。网络功能](#net-functions)

因为。NET 函数，可以拉进 [RabbitMQ NuGet 扩展](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ)。

```
Install-Package Microsoft.Azure.WebJobs.Extensions.RabbitMQ -Version 0.2.2029-beta 
```

Enter fullscreen mode Exit fullscreen mode

#### JavaScript、TypeScript、Python、Java 和 PowerShell 函数

对于其他功能，您需要使用 Azure Functions 核心工具
将扩展安装到项目中

```
func extensions install Microsoft.Azure.WebJobs.Extensions.RabbitMQ -v 0.2.2029-beta 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以创建一个函数，该函数可以在定义的 RabbitMQ 队列中丢弃消息时激活并运行。

#### 示例 C#触发器与输出绑定

```
public static void Run(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "RabbitMqConnection")] string inputMessage,
    [RabbitMQ(
        ConnectionStringSetting = "RabbitMQConnection",
        QueueName = "downstream")] out string outputMessage,
    ILogger logger)
{
    outputMessage = inputMessage;
    logger.LogInformation($"RabittMQ output binding function sent message: {outputMessage}");
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 示例 function.json

```
{  "bindings":  [  {  "type":  "rabbitMQTrigger",  "connectionStringSetting":  "RabbitMqConnection",  "queueName":  "queue",  "deadLetterExchangeName":  "dtx",  "name":  "myQueueItem"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

`connectionStringSetting`解析为一个应用程序设置(在本地开发时为`local.settings.json`，它应该遵循类似于`amqp://user:PASSWORD@127.0.0.1:5672`的格式。

有关如何使用 Azure Kubernetes 服务托管 RabbitMQ 端点进行测试的说明，您可以[遵循本指南](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint)。

## 送还和死信

与 Azure 函数的其他基于队列的触发器一样，RabbitMQ 触发器支持重试和死信。如果执行导致异常，可以重试该消息。一旦消息超过了其重新传递计数阈值，我们将把它路由到定义的死信交换，在那里消息可能会在病毒队列中结束。当填充了 DLX 选项时，扩展会自动将“-poison”附加到原始队列名。

你可以在这里了解更多关于如何配置死信和病毒队列的信息。

## 开放和进化

这只是最初的公开预览版本。像所有 Azure Functions 扩展一样，这个扩展是[完全开源的](https://github.com/azure/azure-functions-rabbitmq-extension)。如果您有任何问题、反馈或请求——请在 GitHub 资源库上提交问题。我们也欢迎来自社区的任何贡献和补充。

我们对这一宣布感到兴奋，并期待更多的事件源与 Azure 功能兼容。
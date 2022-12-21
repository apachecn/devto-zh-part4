# 实施无服务器 Saga Orchestrator

> 原文：<https://dev.to/thdotnet/implementing-a-serverless-saga-orchestrator-50d6>

> 本文是 [#ServerlessSeptember](https://dev.to/azure/serverless-september-content-collection-2fhb) 的一部分。在这个无服务器的内容集合中，您可以找到其他有用的文章、详细的教程和视频。9 月份，每天都有来自社区成员和云倡导者的新文章发布，没错，每天都有。
> 
> 在[https://docs.microsoft.com/azure/azure-functions](https://docs.microsoft.com/azure/azure-functions/?WT.mc_id=servsept_devto-blog-cxa)了解更多关于微软 Azure 如何实现你的无服务器功能。

## 简介

无论你参加哪个科技盛会，都会有人谈论微服务。这可能不适合你，但事实是微服务架构推进了可以独立开发、部署和扩展的松散耦合服务。这种架构风格也给桌面带来了一些挑战，我将在本文中解决其中一个问题。

## 传奇模式

为了确保跨多个服务的数据一致性，我们通常实现 Saga 模式:一系列本地事务。如果某个本地事务由于某种原因失败，Saga 会执行一系列补偿事务，这些事务会撤消之前的本地事务所做的更改。

这个想法并不新鲜，我在很多年前从我的朋友和 Felipe Oliveira 教授(@scaphe)那里了解到它，但当时它被应用于面向服务的架构(SOA)。有两种方法可以实现 Saga 模式:

*   编排:每个本地事务发布触发其他服务中事务执行的域事件；

*   编排:执行的逻辑和顺序存在于一个编排器中，该编排器提交/回滚与该业务逻辑相关的所有阶段；

除了持久函数之外，使用 Azure 函数，您可以轻松实现无服务器的 Saga Orchestrator(使用扇出和/或函数链):

## 样品

为了使这个例子简单，我将在本文中只讨论 orchestrator 逻辑:

```
 public static class OrderSaga
    {
        [FunctionName("OrderSaga")]
        public static async Task<bool> RunOrchestrator(
            [OrchestrationTrigger] DurableOrchestrationContext context)
        {
            var orderId = context.GetInput<Guid>();

            Task<bool> orderResponse = context.CallActivityAsync<bool>("OrderActivity", orderId);
            Task<bool> paymentResponse = context.CallActivityAsync<bool>("PaymentActivity", orderId);

            await Task.WhenAll(orderResponse, paymentResponse);

            if (orderResponse.Result == false || paymentResponse.Result == false)
            {
                await context.CallActivityAsync("RollbackOrderActivity", orderId);
                await context.CallActivityAsync("RollbackPaymentActivity", orderId);

                return false;
            }

            return true;
        } 
```

## 引擎盖下发生了什么？

持久函数公开了一个端点，我们可以在这里监控每个活动的进度和输出。它还保存来自每个活动的输出，并且它具有天生实现的重试逻辑。您所要做的就是将成功/失败逻辑实现到您的活动中，然后使用 orchestrator 提交或回滚每个阶段，就像我们之前所做的那样。

## 更多信息

您可以在以下链接中找到更多信息:

*   微服务:[微服务. io](https://microservices.io)

*   传奇模式:[传奇模式](https://microservices.io/patterns/data/saga.html)

*   持久功能: [Docs Azure](https://docs.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-overview)

*   我的 Github 上有这个例子: [Thdotnet](https://github.com/thdotnet/ServerlessSaga)
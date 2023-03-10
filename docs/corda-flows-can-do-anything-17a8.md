# Corda - Flows 无所不能

> 原文：<https://dev.to/lankydandev/corda-flows-can-do-anything-17a8>

在 Corda 中，流可以做的不仅仅是提议在组织之间记录新的交易。虽然，说他们可以做任何事情可能有点深远(虽然很容易记起)。我真正想说的是，流是节点的入口点。Corda 提供了一系列通过 RPC 与节点交互的功能。目前，这些覆盖了更简单的用例，比如查询 vault，但是所提供的内容是有限制的。流程覆盖任何需要触发的*非标准*逻辑。因此，如果您想从 Corda 节点公开一个 API，让客户端可以触发或使用，那么这篇文章就是为您准备的。

在这篇文章中，我将探索如何使用流作为节点的入口点。提议新事务的流在许多其他教程中都有介绍，但在本文中并未涉及。焦点将只集中在提供不同功能的流程上。

## 理论

正如介绍中提到的，我想重申一下，从 Corda 节点公开端点或 API 的唯一方式是通过流。现有的 RPC 端点将处理*标准*功能。将来这可能会改变，增加对自定义 RPC 端点的支持。但是，到目前为止(Corda 4)，流是在节点内运行非标准逻辑的唯一方式。

在我看来，通过流公开节点的功能非常类似于为 web 服务器编写 HTTP 端点。您必须定义什么是可访问的。客户端不能请求访问 web 服务器内部的任何内部代码。如果没有端点，则会发回一个错误。同样的概念也适用于 Corda。这样，在与节点交互时，推理客户机能做什么或不能做什么就更简单了。

关于实现，最简单的开始方式是向您展示一个例子:

```
@StartableByRPC
class StupidSimpleQueryFlow(private val externalId: String) : FlowLogic<MessageState>() {
    // does not need to be suspendable?
    override fun call(): MessageState {
        return serviceHub.vaultService.queryBy<MessageState>(
            QueryCriteria.LinearStateQueryCriteria(
                externalId = listOf(externalId)
            )
        ).states.single().state.data
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你了解你的东西，那么你会意识到你可以通过 RPC 做到这一点。我选择这个例子是为了让你从一些熟悉的东西开始。您可能在自己的流中做过类似的查询。尽管它们很有可能是一个更广泛的过程的一部分。

这里需要注意一些与大多数流程不同的地方:

*   没有`@InitiatingFlow`标注
*   没有`@Suspendable`标注
*   没有匹配的响应者流

他们为什么失踪了？嗯，他们不需要。想想吧。每个点都与另一个节点的通信相关，而这并没有发生。流程开始，执行查询并返回检索到的数据。让我详细说明一下:

*   `@InitiatingFlow`允许流创建新的会话来与其他节点通信。因为不需要交互，所以不需要创建会话，因此可以删除注释。
*   `@Suspendable`是暂停功能所必需的。允许流创建*检查点*，当流需要再次唤醒时加载这些检查点。流挂起最常见的地方是在与另一个节点通信期间。在这种情况下，可以删除注释，因为流永远不需要暂停。
*   **响应方流**运行在交易方节点上，当您`send`向它们发送数据时，它们与您的流进行交互。同样，没有通信，因此其他节点不需要安装与上面的流配对的流。

大多数不与其他节点交互的流将遵循这种结构(一些流仍然可以挂起，这取决于您正在做什么)。

然后可以从外部客户端访问上面定义的流程:

```
proxy.startFlow(::StupidSimpleQueryFlow, "asdas").returnValue.get() 
```

Enter fullscreen mode Exit fullscreen mode

## 例子

下面是一些你可以写的流的例子，以及为什么你会使用它们。

### 从服务中检索值

```
@StartableByRPC
class GetMeSomeValueFromAService : FlowLogic<Long>() {
  override fun call(): Long {
    return serviceHub.cordaService(IncrementingService::class.java).counter
  }
}

@CordaService
class IncrementingService(serviceHub: AppServiceHub) : SingletonSerializeAsToken() {

  var counter: Long = 0

  init {
    timer(period = TimeUnit.SECONDS.toMillis(1)) {
      counter += 1
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您有一个存储对外部客户机有用的信息的服务，您将需要一种检索它的方法。

### 在节点内部调用代码，减少代码重复

```
@StartableByRPC
class ExecuteSomeInternalNodeLogicYouDontWantToDuplicateFlow(private val recipient: Party) :
  FlowLogic<List<MessageState>>() {
  override fun call(): List<MessageState> {
    return serviceHub.cordaService(MessageRepository::class.java)
      .findAllMessagesByRecipient(recipient)
  }
}

@CordaService
class MessageRepository(private val serviceHub: AppServiceHub) : SingletonSerializeAsToken() {

  fun findAllMessagesByRecipient(recipient: Party): List<MessageState> {
    return serviceHub.vaultService.queryBy<MessageState>(
      QueryCriteria.VaultCustomQueryCriteria(
        builder { MessageSchema.PersistentMessage::recipient.equal(recipient.name.toString()) }
      )
    ).states.map { it.state.data }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

有用的可重用代码片段可以提取到单独的类中。更具体地说，对于 Corda，您可以将逻辑放在流或服务中。

要从客户端执行流或服务中的代码:

*   **在一个流内**——从客户端调用这个流
*   **在服务内部**——添加一个委托给服务的新流

您可以在客户机中实现上面的查询，但是如果您在节点中也使用相同的查询，您将有两个版本。

### 执行不能通过 RPC 完成的逻辑

```
@StartableByRPC
class DoSomethingComplicatedThatYouCantDoViaRpc(private val recipient: Party) :
    FlowLogic<List<MessageSchema.PersistentMessage>>() {
    override fun call(): List<MessageSchema.PersistentMessage> {
        return serviceHub.withEntityManager {
            createQuery(
                "SELECT m FROM $TABLE_NAME m WHERE m.recipient = ?1",
                MessageSchema.PersistentMessage::class.java
            ).setParameter(
                1,
                recipient.name.toString()
            ).resultList
        }
    }

    private companion object {
        val TABLE_NAME = MessageSchema.PersistentMessage::class.jvmName
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如前所述，通过 RPC 可以做的事情是有限的。上面的代码就是一个例子。它访问`EntityManager`,后者可以执行比 vault 所允许的级别更低的查询。

## 结论

这篇文章的简短结论。流是可以运行完全自定义逻辑的节点的唯一入口点。Corda 提供了几个 API 来与节点交互，但是这些 API 提供的功能有限。要执行任何类型的逻辑(一个节点可以运行)，您需要有一个允许进入节点并为您按下按钮的流。

如果你喜欢这篇文章或者觉得它有帮助(或者两者兼而有之),那么请随时在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) ,并且记得与其他任何可能发现这篇文章有用的人分享！
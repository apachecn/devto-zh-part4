# Corda -保存交易，其中只有一部分当事人是签署人

> 原文：<https://dev.to/lankydandev/corda-saving-transactions-where-only-a-subset-of-parties-are-signers-2i5h>

我花了一段时间才想出一个标题，既能概括这篇文章的内容，又不会变成一个完整的句子。我想我已经设法选择了一些清晰的东西😅。不管怎样，让我澄清一下我到底在说什么。

我在 Slack 里见过几个人问类似下面这样的问题:

> 在示例中，当运行响应者流的节点是所需的签署者之一时，它显示了响应者流。但是，如果运行响应者流的节点不是必需的签名者(例如，tx 中涉及的某个状态的参与者之一)，该怎么办呢？我需要为这样的节点编写响应器流吗？如果有，应该怎么写 responder 流？

换句话说:

> 我有一个州有一组参与者。他们中的一些人必须在交易上签字，一些人不能。我如何构建我的流，尤其是响应者流来应对这种情况？

由于响应者流程的工作方式，每个交易对手运行相同的响应者代码(除非被覆盖)。让一组交易对手做一件事，另一组做另一件事，这不是样本中的简单代码*所能处理的。需要构建您的流来显式地处理这一点。*

实现这一点的代码相对简单，但可能不太明显，除非您已经用 Corda 开发了一段时间。

到目前为止，我有两个解决这个问题的方法。我很确定这些是目前可用的最佳解决方案，我想不出任何其他可行或值得追求的方案。这些解决方案是:

*   向交易对手发送一个标志，告诉他们是否是签名人
*   使用子流程收集签名或保存交易

我将在下面几节中展开这些内容。

在我讲到他们之前，如果不考虑签名者和参与者的差异，会发生什么？一个典型的响应流程将包括:

```
@InitiatedBy(SendMessageFlow::class)
class SendMessageResponder(private val session: FlowSession) : FlowLogic<SignedTransaction>() {

  @Suspendable
  override fun call(): SignedTransaction {
    subFlow(object : SignTransactionFlow(session) {
      override fun checkTransaction(stx: SignedTransaction) { }
    })
    return subFlow(ReceiveFinalityFlow(otherSideSession = session))
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果发起流程触发了非签约交易方的响应方，则出现错误:

```
net.corda.core.flows.UnexpectedFlowEndException: Tried to access ended session SessionId(toLong=3446769309292325575) with empty buffer
    at net.corda.node.services.statemachine.FlowStateMachineImpl.processEventsUntilFlowIsResumed(FlowStateMachineImpl.kt:161) ~[corda-node-4.0.jar:?]
    at net.corda.node.services.statemachine.FlowStateMachineImpl.suspend(FlowStateMachineImpl.kt:407) ~[corda-node-4.0.jar:?]
    at net.corda.node.services.statemachine.FlowSessionImpl.receive(FlowSessionImpl.kt:67) ~[corda-node-4.0.jar:?]
    at net.corda.node.services.statemachine.FlowSessionImpl.receive(FlowSessionImpl.kt:71) ~[corda-node-4.0.jar:?]
    at net.corda.core.flows.SignTransactionFlow.call(CollectSignaturesFlow.kt:294) ~[corda-core-4.0.jar:?]
    at net.corda.core.flows.SignTransactionFlow.call(CollectSignaturesFlow.kt:198) ~[corda-core-4.0.jar:?]
    at net.corda.node.services.statemachine.FlowStateMachineImpl.subFlow(FlowStateMachineImpl.kt:290) ~[corda-node-4.0.jar:?]
    at net.corda.core.flows.FlowLogic.subFlow(FlowLogic.kt:314) ~[corda-core-4.0.jar:?]
    at dev.lankydan.tutorial.flows.SendMessageResponder.call(SendMessageFlow.kt:70) ~[main/:?]
    at dev.lankydan.tutorial.flows.SendMessageResponder.call(SendMessageFlow.kt:64) ~[main/:?] 
```

Enter fullscreen mode Exit fullscreen mode

这是因为从未向非签名者发送要签名的事务，但是，唉，他们的代码正坐在那里等待签署一个永远不会到来的事务。多么悲伤😿。我来这里是为了阻止你的交易对手像这里这样悲伤。

这也是一个很好的教学时刻。如果您看到类似上面的堆栈跟踪，很可能是由于放错了`send`和`receive`的位置。要么它们的顺序不对，要么缺少`send`或`receive`。一行一行地检查你的代码，你应该有希望找到不匹配的地方。

## 按旗区分

这个解决方案是我首先想到的，因为它更容易理解。

通知交易对手，告诉他们是否需要签署交易。然后，它们的响应器流将执行`SignTransactionFlow`或者跳过它，直接进入`ReceiveFinalityFlow`。两条路径都将始终收到标志并调用`ReceiveFinalityFlow`。

下面可以找到一个例子:

```
@InitiatingFlow
class SendMessageFlow(private val message: MessageState) :
  FlowLogic<SignedTransaction>() {

  @Suspendable
  override fun call(): SignedTransaction {
    val spy = serviceHub.identityService.partiesFromName("Spy", false).first()

    val tx = verifyAndSign(transaction(spy))

    // initiate sessions with each party
    val signingSession = initiateFlow(message.recipient)
    val spySession = initiateFlow(spy)

    // send signing flags to counterparties
    signingSession.send(true)
    spySession.send(false)

    val stx = collectSignature(tx, listOf(signingSession))

    // tell everyone to save the transaction
    return subFlow(FinalityFlow(stx, listOf(signingSession, spySession))
  }

  private fun transaction(spy: Party) =
    TransactionBuilder(notary()).apply {
      // the spy is added to the messages participants
      val spiedOnMessage = message.copy(participants = message.participants + spy)
      addOutputState(spiedOnMessage, MessageContract.CONTRACT_ID)
      addCommand(Command(Send(), listOf(message.recipient, message.sender).map(Party::owningKey)))
    }
}

@InitiatedBy(SendMessageFlow::class)
class SendMessageResponder(private val session: FlowSession) : FlowLogic<SignedTransaction>() {

  @Suspendable
  override fun call(): SignedTransaction {
    // receive the flag
    val needsToSignTransaction = session.receive<Boolean>().unwrap { it }
    // only sign if instructed to do so
    if (needsToSignTransaction) {
      subFlow(object : SignTransactionFlow(session) {
        override fun checkTransaction(stx: SignedTransaction) { }
      })
    }
    // always save the transaction
    return subFlow(ReceiveFinalityFlow(otherSideSession = session))
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面代码的要点:

*   `spy`(另一方)被添加到状态的`participants`列表中
*   旗帜被送到参与者手中，告诉他们是否要签名
*   响应者传递标志，如果被告知跳过，则跳过
*   `ReceiveFinalityFlow`被调用，等待发起方调用`FinalityFlow`

由于没有什么可说的，我就不解释了。

## 额外发起流程分离逻辑

由于不同的流相互混合导致的间接性，这个解决方案有点复杂。在进行任何解释之前，确实需要阅读此解决方案:

```
@InitiatingFlow
@StartableByRPC
class SendMessageWithExtraInitiatingFlowFlow(private val message: MessageState) :
  FlowLogic<SignedTransaction>() {

  @Suspendable
  override fun call(): SignedTransaction {
    logger.info("Started sending message ${message.contents}")

    val spy = serviceHub.identityService.partiesFromName("Spy", false).first()

    val tx = verifyAndSign(transaction(spy))

    // collect signatures from the signer in a new session
    val stx = subFlow(CollectSignaturesInitiatingFlow(tx, listOf(message.recipient)))

    // initiate new sessions for all parties
    val sessions = listOf(message.recipient, spy).map { initiateFlow(it) }

    // tell everyone to save the transaction
    return subFlow(FinalityFlow(stx, sessions))
  }

  private fun transaction(spy: Party) =
    TransactionBuilder(notary()).apply {
      // the spy is added to the messages participants
      val spiedOnMessage = message.copy(participants = message.participants + spy)
      addOutputState(spiedOnMessage, MessageContract.CONTRACT_ID)
      addCommand(Command(Send(), listOf(message.recipient, message.sender).map(Party::owningKey)))
    }
}

@InitiatedBy(SendMessageWithExtraInitiatingFlowFlow::class)
class SendMessageWithExtraInitiatingFlowResponder(private val session: FlowSession) : FlowLogic<SignedTransaction>() {

  @Suspendable
  override fun call(): SignedTransaction {
    // save the transaction and nothing else
    return subFlow(ReceiveFinalityFlow(otherSideSession = session))
  }
}

@InitiatingFlow
class CollectSignaturesInitiatingFlow(
  private val transaction: SignedTransaction,
  private val signers: List<Party>
) : FlowLogic<SignedTransaction>() {

  @Suspendable
  override fun call(): SignedTransaction {
    // create new sessions to signers and trigger the signing responder flow
    val sessions = signers.map { initiateFlow(it) }
    return subFlow(CollectSignaturesFlow(transaction, sessions))
  }
}

@InitiatedBy(CollectSignaturesInitiatingFlow::class)
class CollectSignaturesResponder(private val session: FlowSession) : FlowLogic<SignedTransaction>() {

  @Suspendable
  override fun call(): SignedTransaction {
    // sign the transaction and nothing else
    return subFlow(object : SignTransactionFlow(session) {
      override fun checkTransaction(stx: SignedTransaction) { }
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码流程如下:

*   `spy`(另一方)被添加到状态的`participants`列表中
*   通过调用`CollectSignaturesInitiatingFlow`来收集签名者的签名
*   `CollectSignaturesInitiatingFlow`创建新会话并调用`CollectSignaturesFlow`
*   `CollectSignaturesResponder`签署`CollectSignaturesInitiatingFlow`发送的交易
*   为每个参与者启动更多的会话
*   调用`FinalityFlow`,触发链接到原始/顶层流的`SendMessageWithExtraInitiatingFlowResponder`

上面的代码基于这样一个事实，即任何标注了`@InitiatingFlow`的流都将被路由到它的`@InitiatedBy`伙伴，并且是在**新会话**中完成的。利用这一点，可以添加一个响应者流，该响应者流只为所需的签名者触发。仍然为顶级流(`SendMessageWithExtraInitiatingFlowFlow`)创建会话，并在`FinalityFlow`中使用。

在幕后还发生了一些其他的事情，但是在这篇文章的上下文中并不需要。

## 哪个好？

现在很难说。我将不得不做一些性能测试，并对代码进行更多的修改…

我目前的观点是**额外的启动流**效果更好一些。它消除了从发起者到每个交易对手的额外网络行程的需要。它添加了一些额外的样板代码，但是也从响应者/对方代码的剩余部分中提取出了签名逻辑。

说实话，正如我一分钟前所说，我真的需要使用它，并提出更复杂的用例，然后才能给出一个好的答案。不过，我怀疑我会抽时间去做这件事…😩

## 结论

无论你走哪条路，或者即使你想出了另一条路，在 Corda 中可以 100%地完成只有一部分当事人签署的交易。如果这不可能，我会非常失望。只要您有一个适当的流程来改变响应者流中的逻辑，以处理签名方和非签名方需要的不同的`send`和`receive`。你应该可以走了。

如果你喜欢这篇文章或者觉得它有帮助(或者两者兼而有之),那么请随时在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) ,并且记得与其他任何可能发现这篇文章有用的人分享！
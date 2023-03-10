# Corda -防止广播状态的无效支出

> 原文：<https://dev.to/lankydandev/corda-preventing-invalid-spending-of-broadcasted-states-2153>

Corda 非常灵活，它允许你将编写许多复杂工作流所需的代码放在一起。这种灵活性有一个缺点。作为开发人员，您需要花一些时间和精力来设计您的应用程序。不再有空白合同。不再有响应者流对它们接收的任何内容进行签名。当您的应用程序在生产中无缝运行时，您会很高兴自己付出了努力。对你来说幸运的是，我在这里保护你的金库免受欺诈交易和无效支出的影响。

在我之前的文章[向外部组织广播事务](https://dev.to/lankydandev/broadcasting-a-transaction-to-external-organisations-200g)中，我向您展示了如何与网络中的任何人共享事务及其状态。增加了国家以不可预见的方式支出的潜在途径。可能会导致 CorDapp 定义的业务流程崩溃。

我给了你在这种情况下结束的知识，现在我正试图阻止你做出愚蠢的事情🤦‍♀️，以及潜在的重大错误。换句话说，我给了你车钥匙，现在我正试图防止你撞车或撞倒别人🚗💀。

或者甚至，

> 🕷的力量越大，责任越大。🕷

在这篇文章中，我将介绍应该添加到契约和流中的检查，以防止其他节点从应用程序中的潜在疏忽中获得优势。更准确地说，防止从广播事务接收的状态以不可预料的方式被花费。做出这些更改后，状态只能由应用程序完全允许的参与方使用。

我将使用我在所有 Corda 帖子中包含的`MessageState` (a `LinearState`)。为清楚起见，添加了以下内容:

```
@BelongsToContract(MessageContract::class)
data class MessageState(
  val sender: Party,
  val recipient: Party,
  val contents: String,
  override val linearId: UniqueIdentifier,
  override val participants: List<Party> = listOf(sender, recipient)
) : LinearState 
```

Enter fullscreen mode Exit fullscreen mode

以这个状态为例，我可以帮助您考虑自己的契约设计和流程中的验证。我们将看到的工作流是回复两个节点之间发送的消息的过程。

## 合同验证

我希望你已经知道这一点。合同验证很重要。这是由事务的每个签名者以及将来需要验证同一事务的任何人运行的验证。国家只能以契约设计允许的方式进化。谁能创造它们，谁能消费它们，它们能有什么价值，每笔交易有多少，等等。由您来定义这些规则。

下面是一些我认为重要的一般要求，以确保合同对州进行必要的限制。引导国家沿着可能的路线创建和转变:

```
override fun verify(tx: LedgerTransaction) {
  val commandWithParties: CommandWithParties<Commands> = tx.commands.requireSingleCommand()
  when (commandWithParties.value) {
    is Commands.Send -> // validation for sending
    is Commands.Reply -> requireThat {
      val inputPublicKeys = tx.inputs.flatMap { it.state.data.participants.map(AbstractParty::owningKey) }.toSet()
      "The input participant keys are a subset of the signing keys" using commandWithParties.signers.containsAll(inputPublicKeys)
      val outputPublicKeys = tx.outputStates.flatMap { it.participants.map(AbstractParty::owningKey) }.toSet()
      "The output participant keys are a subset of the signing keys" using commandWithParties.signers.containsAll(outputPublicKeys)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然这个验证是针对`LinearState`的，但我实际上是从`ContractsDSL.verifyMoveCommand`那里窃取了规则。也许这表明我的`MessageState`实际上是一个`OwnableState`(一个只有一个主人的州)，但是让我们把它藏起来🙈🙊。

上面代码片段中的规则检查事务是否将由输入和输出状态的所有参与者签名。确保所有相关方有机会验证交易。换句话说，流必须包括命令所需签名者中输入和输出状态提到的各方，以及为向他们发送事务而打开的会话。

由于这些规则，如果输入和输出状态的参与者不对事务进行签名，就无法将事务提交给任何人的保险库。从合同的角度来看，这样做是非法的。让我用一种更容易理解的方式来解释。在没有通知原始参与者状态的情况下，不能创建任何消耗`MessageState` s 的事务。此外，未经当事人同意，不得创建回复并将其存储在当事人的保险库中。

这些规则是构建您自己的合同验证的良好基础。明确说明哪一方必须签署交易是证明其有效性的重要要求。可以根据需要添加更多的规则。

例如，可以将以下检查添加到上面的示例中:

```
override fun verify(tx: LedgerTransaction) {
  val commandWithParties: CommandWithParties<Commands> = tx.commands.requireSingleCommand()
  val command = commandWithParties.value
  when (command) {
    is Commands.Send -> // validation for sending
    is Commands.Reply -> requireThat {
      // general requirements from previous snippet
      // precise requirements for `MessageState`
      "One input should be consumed when replying to a message." using (tx.inputs.size == 1)
      "Only one output state should be created when replying to a message." using (tx.outputs.size == 1)
      val output = tx.outputsOfType<MessageState>().single()
      val input = tx.inputsOfType<MessageState>().single()
      "Only the original message's recipient can reply to the message" using (output.sender == input.recipient)
      "The reply must be sent to the original sender" using (output.recipient == input.sender)
      // covered by the general requirements
      "The original sender must be included in the required signers" using commandWithParties.signers.contains(input.sender.owningKey)
      "The original recipient must be included in the required signers" using commandWithParties.signers.contains(input.recipient.owningKey)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这些规则特定于`MessageState`的用例。限制可以回复消息的各方。更准确地说，回复的发送者必须是原始消息的接收者，并且只能被发送回原始发送者。这些规则在这种情况下是有意义的。

即使有这些规定，欺诈交易仍然可以进行。契约根据事务的状态以及状态的内容来检查签名。但是，仍然有一些方法可以解决这些问题。

也就是说，这些情况中的一些只有在交易被发送给最初不参与交换的各方时才会发生。正如我在引言和[向外部组织](https://dev.to/lankydandev/broadcasting-a-transaction-to-external-organisations-200g)广播交易中提到的，这是一种可能出现的情况。

现在我已经做好了准备，让我解释一下上面合同中的一个漏洞。没有什么可以阻止第三方创建对原始消息的模仿回复。在这种情况下，回复的发件人和收件人是有效的，但消息是由其他人创建的，而不是发件人。这有点让人摸不着头脑，所以你可能需要把这句话读几遍。

为了防止这种情况发生，需要在创建该事务的流程中添加一些逻辑。这将是下一节的主题。

在我们继续之前，我想再次强调，这种情况只有在原始事务已经与其他方共享时才会发生。因此，这可能是一种永远不会发生的情况。但是有可能。在这些场景中覆盖你的基础实际上取决于你的用例以及你对用户淘气的偏执程度。

## 流量验证

流为您提供了一个空间来添加位于契约之外的事务验证。这些规则可以更具体地针对单个组织(我在[扩展流程以定制交易验证](https://dev.to/lankydandev/extending-flows-to-customise-transaction-validation-3jf2-temp-slug-7180013)中提到过这一点)，或者依赖于合同中不可用的信息。

在上一节中，我提到了合同验证中存在一些漏洞。下面的响应流程添加了两个约束来修补它们:

```
@InitiatedBy(ReplyToMessageFlow::class)
class ReplyToMessageResponder(private val session: FlowSession) : FlowLogic<SignedTransaction>() {

  @Suspendable
  override fun call(): SignedTransaction {
    val stx = subFlow(object : SignTransactionFlow(session) {
      override fun checkTransaction(stx: SignedTransaction) {
        val message = stx.coreTransaction.outputsOfType<MessageState>().single()
        require(message.sender != ourIdentity) {
          "The sender of the new message cannot have my identity when I am not the creator of the transaction"
        }
        require(message.sender == session.counterparty) {
          "The sender of the reply must must be the party creating this transaction"
        }
      }
    })
    return subFlow(
      ReceiveFinalityFlow(
        otherSideSession = session,
        expectedTxId = stx.id
      )
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

本例中的`require`块不可能来自契约内部，因为它们需要契约不包含的信息。这个额外的背景是至关重要的。该合同只能根据其提供的信息生效。另一方面，该流知道它在提议的事务的接收端，以及谁是发送它的对应方。

使用这个知识，流程可以添加两个新的规则。

*   回复的发送者不能具有流的身份。防止另一方假冒他们的身份并试图代表他们发送回复。
*   回复的发送者必须是交易对手会话的所有者。防止另一方模仿发送者的身份。

尽管第二个规则是第一个规则的超集，但是将它们分离出来可以提供更好的错误消息。如果这些消息对您不重要，那么删除第一个`require`语句是可以的。

由于该契约，事务必须收集这一方的签名才能被持久化。为响应者流提供添加额外验证的机会，并在需要时拒绝验证。随着契约和流作为一个团队工作，流被非法使用的机会大大减少了。

## 结论

由于 Corda 的灵活性，作为一个 CorDapp 开发者，你有责任对你的应用程序进行足够的约束，以防止无效的使用。与未参与原始交互的节点共享事务的能力使这变得更加复杂。如果 CorDapp 没有经过深思熟虑地组合在一起，事务的状态可能会被对状态没有直接所有权的一方使用。在您的合同和流程中发现的验证对于防止这些场景的发生是至关重要的。

然而，重要的是要记住它确实取决于您的用例。也许你想允许当事人从与他们共享的交易中花费不属于他们的状态。在这种情况下，你可以放宽科达普的限制。只要它是设计好的而不是疏忽。

如果你喜欢这篇文章或者觉得它有帮助(或者两者兼而有之),那么请随时在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) ,并且记得与其他任何可能发现这篇文章有用的人分享！
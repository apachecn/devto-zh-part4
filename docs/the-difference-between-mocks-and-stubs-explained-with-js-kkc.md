# mocks 和 stubs 的区别，用 JS 解释

> 原文：<https://dev.to/snird/the-difference-between-mocks-and-stubs-explained-with-js-kkc>

存根和模拟是测试中经常被误解的两个基本概念。以至于我们有了马丁·福勒关于这个主题的著名文章，还有许多关于这个问题的 stackoverflow 问题。
马丁斯的文章对于缺乏耐心的现代读者来说是一篇很长的文章，有点跑题，而且在当前的炒作语言 JS 中没有例子。我会尽量让我的解释更简洁。

我将从标题定义开始:
**存根和模拟都是测试的虚拟对象，而存根只实现预编程的响应，模拟也预编程特定的期望。**

要将其放入工作流程:

### 存根

*   **设置** -定义存根本身，程序中的什么对象以及如何存根
*   **练习** -运行您想要测试的功能
*   **验证** -检查存根中的值是否符合预期
*   **拆卸** -如果需要，进行清理。例如，时间存根通常是全局的，你需要放弃控制权

### 模仿

*   **设置对象** -定义模仿，模仿什么对象以及如何模仿(类似于存根)
*   **设置期望** -定义你期望这个模拟会发生什么
*   **练习** -运行您想要测试的功能
*   **验证模拟** -验证模拟预期得到满足。在一些 JS 库中，这种情况会自动发生，不需要额外的调用，模拟期望会自我验证，如果需要的话会抛出。(主要在异步测试时使用)。
*   **验证** -验证对模拟结果的任何额外期望
*   **拆卸** -如果需要，进行清理。

### 模仿 JS 社区中的&树桩

在我们进入代码之前，因为我的例子将在 JS 中，这里有一个重要的注意事项。

JS 社区中最成功的测试库之一是 [jest](https://jestjs.io) 。但是我不会在我的例子中使用它，原因很简单，jest 是固执己见的，并且不实现模仿。
他们在库中称之为`mock`，实际上是定义上的存根。你不能对模仿本身抱有期望，而只是观察它的行为，并在此基础上提出期望。

我将使用`sinon.js`来演示这个概念，它实现了 mocks 和 stubs 的概念。

### 存根示例

对于我们的例子，我们将单元测试一个虚构的电子商务网站的购物功能。我们将尝试支付并获得付款状态，如果我们成功了，我们将发送一封邮件。

```
const purchaseItemsFromCart(cartItems, user) => {
  let payStatus = user.paymentMethod(cartItems)
  if (payStatus === "success") {
    user.sendSuccessMail()
  } else {
    user.redirect("payment_error_page")
  }
}

}
"when purchase payed successfully user should receive a mail" : function() {
  // Setup
  let paymentStub = sinon.stub().returns("success")
  let mailStub = sinon.stub()
  let user = {
    paymentMethod: paymentStub,
    sendSuccessMail: mailStub
  }

  // Exercise
  purchaseItemsFromCart([], user)

  // Verify
  assert(mailStub.called)
} 
```

### 模仿例子

现在让我们做同样的事情，使用模拟。

```
"when purchase payed successfully user should receive a mail" : function() {
  // Setup objects
  let userMock = sinon.mock({
    paymentMethod: () => {},
    sendSuccessMail: () => {}
  })

  // Setup expectations
  userMock.expect(paymentMethod).returns("success")
  userMock.expect(sendSuccessMail).once()

  // Exercise
  purchaseItemsFromCart([], user)

  // Verify mocks
  userMock.verify()
} 
```

## 伟大。我应该在什么时候使用它们？

这是一个有趣的问题。
还有很多争论——`jest`背后的人决定不实现经典的模拟功能是有原因的。

模仿可以做 stubs 能做的任何事情，还可以直接在它们模仿的对象上设置期望。这给大型测试带来了可读性问题，并且倾向于在测试中期待和测试虚假的对象，而这并不是测试的唯一目的，这使得它成为一个过于关注内部的白盒测试。

这甚至在 sinon 文档中作为何时使用 mock 的指南被提及:

```
Mocks should only be used for the method under test.
In every unit test, there should be one unit under test. 
```

因此，为了不过度使用这个功能并创建一个混乱的、可能是错误目的的测试，您应该将测试中的 mocks 使用限制在一个对象上。

或者，你知道，你可以使用 jest，通过不在第一时间实现这种模拟来使你远离这个决定。
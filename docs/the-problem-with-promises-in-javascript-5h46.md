# JavaScript 中承诺的问题是

> 原文：<https://dev.to/craigmichaelmartin/the-problem-with-promises-in-javascript-5h46>

最近在 Node 呆了很长时间，我不断遇到 3 个反复出现的承诺问题:

*   承诺有一个 API，它鼓励偶然危险的代码
*   Promises 缺乏一个方便的 API 来安全地处理数据。
*   承诺混合了拒绝的承诺和意外的运行时异常

虽然`await`语法是这种语言令人愉快的补充，也是这些问题解决方案的一部分，但它的价值——增加可读性和保持对原始调用栈的控制(即允许返回)——与后两个问题无关，有时只是缓解了第一个问题。

## 承诺有一个 API，它鼓励随意编写危险的代码。

让我们举一个保存用户的例子:

```
// Promises (without using await)
// Casually dangerous code
const handleSave = rawUserData => {
  saveUser(rawUserData)
    .then(user => createToast(`User ${displayName(user)} has been created`))
    .catch(err => createToast(`User could not be saved`));
}; 
```

这段代码看起来清晰易读:一条清晰定义的成功和失败之路。

然而，在试图明确的同时，我们不仅将我们的`catch`附加到了`saveUser`请求上，还附加到了成功路径上。因此，如果 then 抛出(例如，displayName 函数抛出)，那么用户将被通知没有用户被保存，即使保存了。

> 有人可能会想，让我们交换一下 then/catch 的顺序，这样 catch 就直接附加到 saveUser 调用上了。这引入了另一个问题，我们将在下面进一步研究(作为第三个问题)。

使用 await 不一定有帮助。正确使用 API 是不可知的，由于它的块作用域，它也使得危险地编写它变得更容易和更漂亮，如上:

```
// Promises with Async/Await doesn't necessarily help
// Casually dangerous code
const handleSave = async rawUserData => {
  try {
    const user = await saveUser(rawUserData);
    createToast(`User ${displayName(user) has been created`);
  } catch {
    createToast(`User could not be saved`));
  }
}; 
```

由于块范围的原因，在 try 中包含 createToast 行会更方便，但是这段代码也有上面的问题。

使用本机承诺的负责任的重构*看起来*更糟糕/丑陋/糟糕/复杂。让我们先看看不使用`await`的情况。

对于不使用`await`的情况，正确顺序的两个匿名函数(错误函数优先？成功功能第一？)必须被传递给 then，这比使用一个显式的`catch`块:
更没有条理

```
// Promises done responsibly _look_ worse/ugly/bad/complicated :(
const handleSave = rawUserData => {
  saveUser(rawUserData)
    .then(
      user => createToast(`User ${displayName(user)} has been created`),
      err => createToast(`User could not be saved`));
    );
}; 
```

需要说明的是，这本身并不是一个糟糕的 API。但是考虑到作为开发人员显式的正当意图，有一种诱惑是为每个回调使用一个命名函数，而不是两个回调使用一个`then`。负责任的代码不如危险的代码清晰易读- **误用 API 是非常危险的-尽管感觉更加清晰易读！**

使用`async` / `await` *的负责任的重构看起来更加*错误/丑陋/糟糕/复杂。不得不在更高的范围内定义变量感觉像是一个糟糕的控制流。感觉我们在和语言作对:

```
// Promises done responsibly _look_ worse/ugly/bad/complicated :(
const handleSave = async rawUserData => {
  let user;
  try {
    user = await saveUser(rawUserData);
  } catch {
    createToast(`User could not be saved`));
  }
  createToast(`User ${displayName(user)} has been created`);
}; 
```

注意上面的代码甚至不正确。我们需要从`catch`返回(这是我试图避免的，因为它会进一步混淆控制流——特别是如果有 finally 的话),或者如果一个`if (user) { /*...*/ }`块创建了另一个块，那么在 try 之后包装所有东西。感觉我们在上坡工作。

* * *

同样值得注意的是，API 是*也是*不直观的(但这次是另一种方式！)链接多个`then`时。

上面的例子是危险的，因为`catch`是要附加到“根”异步调用(HTTP 请求)——**上的，还有一个危险是，长串的思考让`catch`与最近的那个相关联。**

(它既不附在根承诺上，也不附在最近的承诺上——它附在它前面的整个链上。)

例如:

```
// Casually dangerous code
const userPostHandler = rawUserData => {
  saveUser(rawUserData)
    .then(sendWelcomeEmail)
    .catch(queueWelcomeEmailForLaterAttempt)
}; 
```

与负责人相比，哪个看起来和读起来干净:

```
// Promises done responsibly _look_ worse/ugly/bad/complicated :(
const userPostHandler = rawUserData => {
  saveUser(rawUserData)
    .then(user =>
      sendWelcomeEmail(user)
        .catch(queueWelcomeEmailForLaterAttempt)
    );
}; 
```

* * *

让我们进一步看上面的例子，看看 API 最后一种偶然危险的方式:如果用户不能被创建，让我们添加日志:

```
// Dangerous code
const userPostHandler = rawUserData => {
  saveUser(rawUserData)
    .catch(writeIssueToLog)
    .then(sendWelcomeEmail)
    .catch(queueWelcomeEmailForLaterAttempt)
}; 
```

我们想要的是，如果用户保存失败，将问题写入我们的日志。

但是，因为我们的 catch 不重新抛出或显式拒绝，所以它返回一个解析的承诺，所以下一个 then(sendwelcomemail)将运行，因为没有用户，它将抛出，我们将为一个不存在的用户创建一个排队的电子邮件。

**随意承诺 API 使得意外恢复异常变得容易/流畅/优雅。**

同样，修复看起来很糟糕:

```
// Promises done responsibly _look_ worse/ugly/bad/complicated :(
const userPostHandler = rawUserData => {
  saveUser(rawUserData)
    .then(
      writeIssueToLog,
      user =>
          sendWelcomeEmail(user)
            .catch(queueWelcomeEmailForLaterAttempt)
      );
}; 
```

总结这一节，我们已经看到 promise 处理错误的 API 虽然看起来很时髦，但却很危险:这是由于可读性和从`then`中单独捕获的便利性(即，使用显式的 catch 函数——如果在一个链中，它不仅包括来自“根”承诺的错误，也包括来自最近承诺的错误，还包括来自链中任何承诺的错误)，以及通过促进错误的无意恢复。

虽然添加了`async`操作符会有所帮助，但这是在 try 范围内进行的——使得正确的代码看起来杂乱无章，而不负责任的代码(在 try 中放置了太多内容)看起来更干净/更流畅。

我更喜欢一个 API，它至少可以优化负责任行为的美观性和可读性(通过与语言一起工作),并且最好能排除不负责任或偶尔危险的代码。

## 承诺缺乏一个方便的 API 来安全地处理数据。

在上一节中，我们看到了现有的 promise API 是多么危险(使用两个显式命名函数，而每个函数使用一个匿名参数)，以及它是如何无意中从错误中恢复的。

第二种情况是一个问题，因为 promise API 没有提供更多的助手。

在上面的最后一个例子中，我们的`.catch(logError)`无意中解决了错误，我们真正想要的是别的东西:一个针对错误的`tap`副作用函数。

## 承诺混合了被拒绝的承诺和意外的运行时异常

除了 API 是如何构造的之外，承诺还有另一个主要缺陷:**它们在相同的“路径”中处理无意的本机运行时异常和有意拒绝的承诺——这是两种截然不同的意图。**

```
const userPostHandler = rawUserData => {
  saveUser(userData)
    .then(() => response.send(204))
    .then({email} => postEmailToMailChimp(email))
    .catch(logError)
}; 
```

这段代码想要表达的意思非常简单。(如果有问题，我想保存一个用户并将其电子邮件发布到我的 mailchimp 列表和日志中)。

然而，我不小心把函数名打成了“Mailchimp”而不是“MailChimp”——而不是在开发时提醒我运行时错误——我现在不得不希望我查看日志——这是为了解决 MailChimp 问题，而不是基本的编程问题！

在解释承诺的根本问题时，我稍微简化了行为:承诺将所有错误(不仅仅是固有错误)视为拒绝的承诺。将`throw`和`Promise.reject`视为同义词似乎是合理的。不合理的是使用这一条“路径”来处理两个世界——不加区分的不同“类型”的错误:“战略性”错误(例如`saveUser(user)`抛出一个自定义完整性错误)和基本的 javascript 运行时错误(例如 saveUsr(user)出现一个输入错误并抛出一个 ReferenceError)。这是两种根本不同的现实，但它们在同一条“拒绝承诺”的道路上捆绑在一起。

对于承诺，实际上有三种路径:数据“路径”、非本地错误“路径”(例如，自定义、业务逻辑错误)和本地错误“路径”，但是 API 没有进行这种区分:并且对所有错误和拒绝的承诺都一视同仁。

* * *

[两次更新]

[更新]本文之前继续了“更好”承诺的理论部分...“接下来是一个(从这些问题的无数解决方案中——可能是一个非常糟糕的方案)关于什么可能是解决方案的思想实验..它变成了一个图书馆。”如果你感兴趣，你可以在这里阅读，[让 JavaScript 变得更有功能性](https://dev.to/craigmichaelmartin/making-javascript-promises-more-functional-jp3)

[更新] [Mike Sherov](https://twitter.com/mikesherov) 很友好地回复了一条关于这篇文章的推文，并提供了他对此的看法:我低估了`async` / `async`语法的价值(它抽象出了棘手的`then` / `catch` API，并让我们回到“正常”流程)，剩下的问题(即糟糕的错误处理)是 JavaScript 本身的问题(TC39 一直在发展)。我对这个想法进行了扩展，包括创建一个非常简单的库，在[中使 wait 在 Javascript](https://dev.to/craigmichaelmartin/making-await-more-functional-in-javascript-2le4) 中更加实用
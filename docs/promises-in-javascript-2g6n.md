# Javascript 中的承诺

> 原文：<https://dev.to/geoffreyianward/promises-in-javascript-2g6n>

默认情况下，Javascript 是单线程的。这意味着它必须同步处理所有操作，一次一个。这并不理想，因为我们经常想同时做许多不同的操作。这被称为异步执行任务。

承诺是 Javascript 中处理异步操作的一种方式。最好在处理多个异步调用时使用它们，因为这会导致“回调地狱”，这是一种容易出错的不可管理的状态。承诺在几个重要方面改进了传统的事件回调。它们显著提高了可读性。他们对自己的控制流更加诚实，这使得他们在处理异步任务时更加高效。在捕捉和记录错误时，承诺也更清晰、更有效。

承诺中有 4 个主要的**状态**:

*   **完成**:与承诺相关的动作成功
*   **拒绝**:与承诺相关的动作失败
*   **待定**:承诺仍处于待定状态，即尚未履行或拒绝
*   **已结算**:承诺已履行或拒绝

承诺的参数:

*   Promise 接受一个参数，一个回调函数。
*   回调函数有两个参数， **resolve** 和 **reject**
*   成功的条件将触发解决方案
*   如果有错误，这将触发拒绝

我们可以通过下面的语法来看看承诺的功能:

```
var promise = new Promise(function(resolve, reject) {
  // do a thing, possibly async, then…

  if (/* successful condition */) {
    resolve("Success!");
  }
  else {
    reject(Error("Failure!"));
  }
});
```

这代表了一个单一的条件承诺。这本身工作得非常好，但是当我们开始将多个条件链接在一起时，承诺才真正开始发光。所以我们来看看连锁！

使用。然后，我们可以将多个步骤链接在一起，形成多个任务。每个。那么 stage 代表我们希望 promise 执行的任务。我们把车尾放在链条的末端，标记为。catch，它将处理在它之前链接的所有条件的错误。这种简单性和可读性是人们喜欢使用承诺的核心原因。

这是一个特别长的承诺。即使采取了所有这些步骤，承诺仍然保留了令人惊讶的可读性:

```
asyncThing1().then(function() {
  return asyncThing2();
}).then(function() {
  return asyncThing3();
}).catch(function(err) {
  return asyncRecovery1();
}).then(function() {
  return asyncThing4();
}, function(err) {
  return asyncRecovery2();
}).catch(function(err) {
  console.log("Don't worry about it");
}).then(function() {
  console.log("All done!");
})
```

即使上面的承诺中发生了所有不同的异步操作，我们仍然可以在承诺解析其异步任务时跟随它。

这些只是 Javascript 承诺的许多用途中的一部分。既然承诺是原生 javascript 环境的一部分，我们可以期待看到它们越来越多地被实现。
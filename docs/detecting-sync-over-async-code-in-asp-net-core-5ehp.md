# 在 Async 内核中检测异步代码上的同步

> 原文：<https://dev.to/codeopinion/detecting-sync-over-async-code-in-asp-net-core-5ehp>

[![Sync over Async](img/a1e0a0652bdd6272a6724b339d409b4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vKl6G4MA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeopinion.com/wp-content/uploads/2019/09/lock-300x300.png)

编写一些糟糕的异步代码非常容易，尤其是当您第一次开始使用 async/await 时。Async/await 在。NET，这意味着它通常会一直通过堆栈。如果你试图将 async/await 添加到一个现有的应用程序中，这可能是一个挑战，你通常会在 async 代码上添加 sync。

如果您没有正确使用 async/await，并最终编写了异步同步代码，您最终会导致线程池饥饿。

## 异步同步

该术语指的是进行异步调用，但不等待它。通常这是由调用**引起的。等待()**或者**。返回的**任务**的结果**。
# 使用错误报告服务和无服务器功能时要小心

> 原文：<https://dev.to/jalerson/be-careful-when-using-error-reporting-services-and-serverless-functions-5ei9>

首先，免责声明:这篇博文提出的问题发生在使用 AWS Lambda、Honeybadger 和 Ruby 时。如果您使用不同的云提供商、错误报告服务和/或编程语言，这可能不是问题，**这并不意味着您不应该注意到它**。

话虽如此，我们还是开始吧。

Honeybadger 是一个受欢迎的服务，像许多其他服务一样，用于监控错误和应用程序正常运行时间。我们在生产中使用它已经很长时间了，它处理得很好，但是，当我们开始使用 Honeybadger 报告我们的 AWS Lambda 函数中的最终问题时，一个非常有趣和意想不到的情况发生了。

我们开始注意到 Honeybadger 最终未能从我们的 lambda 函数中报告错误，这非常棘手，因为我们依赖它在出错时通知我们。如果没有通知，就没有问题，对吗？我们有更重要的事情要做，而不是整天在 Cloudwatch 看日志。

本周，我故意在一个特定的 lambda 函数中引发错误，只是为了检查 Honeybadger 是否会报告错误，但它没有。

根据 Honeybadger 文档([架构深度剖析](https://docs.honeybadger.io/lib/ruby/gem-reference/architecture.html))，负责从`Queue`中拉出`Notice`的`Worker`运行在不同的线程中，以便在报告错误时不会阻塞应用程序的执行。这很好，但是，当在无服务器函数中运行时，这是一个问题。

[AWS Lambda 执行上下文](https://docs.aws.amazon.com/lambda/latest/dg/running-lambda-code.html)页面说:

> 如果 AWS Lambda 选择重用执行上下文，则 Lambda 函数启动的后台进程或回调在函数结束时不会完成。您应该确保代码中的任何后台进程或回调在代码退出之前都已完成。

这意味着，当您的 lambda 函数完成执行时，在不同线程中运行的`Worker`可能还没有完成发送错误通知。

幸运的是，解决方法很简单。然而，我花了一些时间才找到它，因为直到我写这篇文章的时候，它还没有出现在 Honeybadger 文档中(是的，我需要深入研究他们的代码)。

当使用`Honeybadger.notify`方法时，确保设置`sync: true` :

```
Honeybadger.notify(error, sync: true) 
```

Enter fullscreen mode Exit fullscreen mode

使用`sync: true`时，Honeybadger 会立即发送通知，而不会将其推入队列由 worker 处理:[https://github . com/honey badger-io/honey badger-ruby/blob/master/lib/honey badger/agent . Rb # L161](https://github.com/honeybadger-io/honeybadger-ruby/blob/master/lib/honeybadger/agent.rb#L161)

或者，也可以在调用`Honeybadger.notify`后调用`Honeybadger.flush`来强制 Honeybadger 发送所有通知:[https://github . com/honey badger-io/honey badger-ruby/blob/master/lib/honey badger/agent . Rb # L314](https://github.com/honeybadger-io/honeybadger-ruby/blob/master/lib/honeybadger/agent.rb#L314)

如果你想进一步了解这件事，可以看看 DZone 上这篇不错的文章:[用 AWS Lambda 进行多线程编程](https://dzone.com/articles/multi-threaded-programming-with-aws-lambda)
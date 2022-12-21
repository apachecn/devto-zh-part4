# 使用 Unref()解除阻塞节点

> 原文：<https://dev.to/pimterry/unblocking-node-with-unref-fh6>

Node.js 在一个[事件循环](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)上运行。它保存了一个要运行的任务队列，并一个接一个地运行它们。新任务在队列运行时出现在队列中，由您的代码(setTimeout)或外部事件(网络连接)添加，该过程简单地继续，直到队列为空。

这一切都很好，直到它不是。

偶尔你会想打破这种模式。如果您希望无限期地按固定时间间隔运行计划任务，会发生什么情况？通常情况下，生活会变得困难:您需要在这段时间内包含和管理一个显式的关闭过程，如果您忘记关闭它，那么该过程将永远运行，没有任何解释。哎哟。

我在研究[mock TTP](https://github.com/httptoolkit/mockttp)(HTTP 工具包背后的 HTTP 拦截&测试库)时遇到了这个问题。Mockttp 需要跟踪您当前的本地 IP 地址，以帮助检测和警告请求循环。这些数据可能会偶尔改变，所以它需要每隔一段时间轮询一次，但是除了其他事情之外，还必须记住小心地关闭该进程，这非常令人恼火。

幸运的是，事实证明您可以轻松地解决这个问题！输入 unref:

## 超时。Unref()

Node.js 中的`setInterval`和`setTimeout`等定时器函数返回一个[超时对象](https://nodejs.org/api/timers.html#timers_class_timeout)，代表正在进行的定时器。

这些可以传递给`clearInterval`或`clearTimeout`来完全关闭定时器，但是它们也有一个很少使用的`unref()`方法。这做了一些神奇的事情:它继续运行您的代码，但阻止它保持进程的活力。像这样:

```
// Update my data every 10 seconds
const interval = setInterval(() => updateMyData(), 10000);
// But don't let that keep the process alive!
interval.unref();

// Log a message if the app is still running 10 seconds from now
const timeout = setTimeout(() => console.log('Still going'), 10000);
// But still shutdown cleanly if it wants to stop before then:
timeout.unref(); 
```

这就像一个你可以在定时器上设置的标志，将它们标记为节点不需要等待的任务。当进程还活着的时候，它们会正常运行，但是如果事件队列的其余部分是空的，那么它们会被忽略，进程仍然会退出。

您也可以使用`timer.ref()`再次将计时器标记为重要，或者(仅在节点 11+中)使用`timer.hasRef()`检查它当前是否配置为阻止进程退出。

如果你想看到这一点，你可以在这里查看对 Mockttp 的修复:[https://github . com/httptoolkit/mock TTP/blob/master/src/util/socket-util . ts # L58-L71](https://github.com/httptoolkit/mockttp/blob/master/src/util/socket-util.ts#L58-L71)

## 明白了

这里最后三样东西一文不值:

*   虽然这可以让你跳过复杂的清理过程，但这并不意味着它们毫无价值。特别是当你的定时器正在做一些昂贵的事情时，提供一个明确的关闭命令是非常有用的。这不能代替你自己打扫卫生！
*   这可能会带来很小的性能代价，因为它实际上是使用一个单独的调度任务来实现的。使用少数几个是好的，但是如果您创建了大量这样的文件，您可能会看到性能影响。
*   你不应该到处都用这个。如果你在你关心的超时上使用这个，你会发现你的应用程序在中途意外退出，比你预期的要早。这类似于弱地图:它是针对特定情况的工具，而不是每天的选项。

既然你在这里，如果你喜欢 Node &想增强你的调试技能，看看 **[HTTP Toolkit](https://httptoolkit.tech/view/javascript)** 。一键 HTTP(S)拦截&调试任何 Node.js 脚本、工具或服务器(以及许多其他工具)。
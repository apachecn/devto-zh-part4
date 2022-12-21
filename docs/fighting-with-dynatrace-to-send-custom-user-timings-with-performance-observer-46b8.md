# 使用性能观察器发送自定义用户计时

> 原文：<https://dev.to/justinribeiro/fighting-with-dynatrace-to-send-custom-user-timings-with-performance-observer-46b8>

我会尽我所能跟踪网络性能。这对最终用户很重要。这对开发者来说很重要。你应该去做。作为网络社区和浏览器供应商，我们有一些令人惊叹的工具和 API，它们大大简化了这一过程，并且每天都在变得越来越好。

收集实时用户数据可能是一个挑战，尤其是如果你使用的是一个大型的第三方库，它可能并不完全做你认为它在做的事情。

这让我想到了处理 Dynatrace。我说“处理”，因为它已经成为我的痛苦太久了。对于那些不熟悉应用程序性能管理(APM)世界的人来说，这个领域的许多竞争对手都有 JavaScript 库，这些库有着良好的意图，但包装在浏览器如何工作的扭曲和过时的视图中。不相信我？看看 Patrick 的第三方网站影响列表，告诉我不同的看法。

> “Justin，Dynatrace 不在那个列表中”。由于 Dynatrace 库的设置方式，很难在 Chrome UX 报告中看到它，它也没有出现在上面的链接中。我对这个*进行了严格的跟踪*，假设我们在一个生产环境中对一个这样的组件进行了 80 毫秒的基线工作，那么在跟踪时，Dynatrace 的开销接近 350 毫秒。在移动设备上，这种影响非常明显，我通过在线程空闲后加载它来解决这个问题(与 Dynatrace 的建议相反)。

但是我跑题了。如何处理自定义用户计时，以及如何将它们发送到 Dynatrace？

用户计时 API 是一个漂亮的小平台特性，用来标记和测量你的代码中可能对你很重要的某些方面。我经常使用它们来确定不同的部分、模块和 web 组件是如何加载的。设置一些标记并测量:

```
performance.mark("my-comp-start"); someThingToMeasure(); performance.mark("my-comp-end"); performance.measure('was-i-fast', "my-comp-start", "my-comp-end"); 
```

你可能已经得出了这样的结论:“好吧，如果我能设置它们，难道我不能把它们发送到任何我想要的远程监测系统吗？“三倍字数，的确可以。

这些标记和量度可以通过各种方法进一步显现。一个这样的例子是通过`PerformanceObserver()`这样做，然后发送到我们的遥测服务:

```
const observer = new window.PerformanceObserver(list =\> { const \_\_sendPerfData = (entry) =\> { // ... send to telemetry }; const entries = list.getEntries(); for (let i = 0; i \< entries.length; i++) { // try to be smart about this if ('requestIdleCallback' in window) { requestIdleCallback( () =\> { \_\_sendPerfData(entries[i]); }, { timeout: 2000, }, ); } else { // fallback scenario \_\_sendPerfData(entries[i]); } } }); // the types of entries we want to listen for observer.observe({ entryTypes: ['mark', 'paint', 'measure'] }); 
```

> 注意:如果我没有提到当你在 DevTools 中运行跟踪时，这些度量和标记也会出现(你在运行跟踪，不是吗？)并且也可以在 Lighthouse 中出现(因为你总是在测量对吗？).

如何将上述信息发送给 Dynatrace？如果我们查看他们的 JavaScript 库的文档，我们什么也没找到。事实证明，这不是公开信息。没有维基。没有文档网站。没有。如果你没有一个 Dynatrace 的账户和适当的权限，你实际上不能得到所说的文档或例子。当你最终获得访问权限时，你必须下载一个 zip 文件，我只能把它描述为乏善可陈的文档。这可能是太好了。

这些文件和例子还有许多不足之处。在通过描述很少的方法进行筛选之后，我们选定了两个:`enterAction`和`leaveAction`。参数看起来足够直接(一些标签和数字代表毫秒)，所以我们在`PerformanceObserver()`中设置了`__sendPerfData()`方法，然后我们开始:

```
if (window.dtrum) { const dtrumId = window.dtrum.enterAction( 'WcTelemetry', entry.mark, entry.startTime, entry.entryType, ); const endTime = entry.duration || entry.startTime; window.dtrum.leaveAction(dtrumId, endTime, entry.startTime); } 
```

事实证明，这是行不通的。请求 Dynatrace 支持会导致毫无结果的对话和 Fiddler traces，最终结果是“您的代码是正确的，库加载错误”。这时，他们会要求您在文档的开头注入价值 18K 的 JavaScript。

想象一下我的快乐。或者我把椅子扔出窗外。这是选择你自己的冒险的人。后者更接近现实。

事实证明，18K 的注入代码也不起作用，除了使性能数字在我的跟踪中看起来更差(让我感到惊讶)。这让我思考:向服务发送数字有多难？因此，开始调试他们可怕的库的旅程。你看，调用任何代码时，动态跟踪库都不会抛出错误。它会运行他们的代码一整天，从来没有发送这些数字。

挖掘他们的精简代码揭示了一个调用堆栈，让我考虑成为一个酒鬼。在这一点上，同事 Dave 指出，唯一可行的方法是使用他们的`now()`方法，这在实践中对我们毫无用处。看一下`now()`就会发现它从纪元开始就使用毫秒。这就成了第一条线索:图书馆并不从我们的表演条目中理解 DOMHighResTimeStamp，它想要的是那个时代的老派时光。

快速修改代码，我们可以在线路上看到数据:

```
if (window.dtrum && entry.entryType === 'measure') { // performance.timeOrigin comes from High Resolution Time Level 2\. // performance.timing is the legacy and deprecated by Navigation Timing Level 2 specification. const timeOrigin = performance.timeOrigin || performance.timing.navigationStart; const dtrumId = window.dtrum.enterAction( 'WcTelemetry', entry.name, timeOrigin + entry.startTime, entry.entryType, ); const endTime = entry.duration + entry.startTime + timeOrigin; window.dtrum.leaveAction(dtrumId, endTime, entry.startTime + timeOrigin); } 
```

除此之外，一切都不顺利。你看，Dynatrace 会发送这些数据，但是这些数据不会出现在任何地方。原来 Dynatrace 也根本不支持高解析时间*。*也就是说现在我们要`trunc()`一些东西:

```
if (window.dtrum && entry.entryType === 'measure') { const timeOrigin = performance.timeOrigin || performance.timing.navigationStart; const dtrumId = window.dtrum.enterAction( entry.name, 'WcTelemetry', Math.trunc(timeOrigin + entry.startTime), ); const endTime = Math.trunc(entry.duration + entry.startTime + timeOrigin); window.dtrum.leaveAction(dtrumId, endTime, Math.trunc(entry.startTime + timeOrigin)); } 
```

是啊，trunc。糟糕的是，可惜代码起作用了，自定义用户计时开始出现了。

总而言之:

1.  Dynatrace 不支持来自自定义用户计时的 DOMHighResTimeStamp 如果您尝试，它永远不会出错，但是您在 Dynatrace 中的仪表板永远不会填充。
2.  Dynatrace 很乐意发送高精度的数字，但不会实际存储它们(同样，我们没有任何关于这个错误或文档方面的指示)。
3.  假设你有文件，这些信息都不在里面。
4.  假设你有一个支持合同，这将被证明是绝对没有价值的任何尝试调试。

我怀疑 Dynatrace 是否有懂网络的人。这听起来像是严厉的批评，但现在是 2019 年，你的 JavaScript 库很重，压下了注重性能的代码，你的文档是垃圾。

如果你错过了，我不会再拥有它了。

如果我们不追究第三方供应商的责任，情况不会好转。做得更好。建立一个更快的网络。如果您不这样做，那么，总会有其他供应商着眼于性能和未来。
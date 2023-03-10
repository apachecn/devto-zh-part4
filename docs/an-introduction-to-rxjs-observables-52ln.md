# RxJs 可观测量简介

> 原文：<https://dev.to/aturingmachine/an-introduction-to-rxjs-observables-52ln>

# 了解 RxJs

反应式编程可能非常难以理解。这里有一个 RxJs 的快速介绍，希望能让你开始使用反应式编程。

## 什么是可观测？

最简单的形式是，一个可观察对象是一个异步操作的结果。我们可以用它们来代替许多异步任务的承诺。然而，Observable 允许我们只用几行代码就能在异步数据流上完成复杂的逻辑。

## 基础知识

在我们开始用可观测量做复杂的工作之前，我们应该先了解基础知识。有很多辅助方法可以创建 Observables，对于这个例子，我们将使用`interval(period: number)`。它创建一个可观察对象，每隔`period`毫秒返回一个递增的数字。创造这种可观察性很简单:

```
interval(1000); 
```

Enter fullscreen mode Exit fullscreen mode

这个可观察值将“发出”，这个术语用于当一个可观察值产生一个新值时，下面的`1 (one second) 2 (one second) 3...`。

### 订阅

为了从上述可观测值中获得发射值，我们需要“订阅”它。

```
const source = interval(1000);
source.subscribe(value => {
  console.log(value);
}); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码将打印出从可观察对象发出的值。

### 河流的想法

我发现，在研究可观测物时，把可观测的“源”想象成一条河通常会有所帮助，每次发射都是一艘顺流而下的船。通过订阅一个观察，我们可以看到河上的船只。接下来，我们将学习如何操纵那些船，如何以及何时被观察河流的人察觉。

## 管道操作员

在这里，我们将进入更复杂的事情，我们可以用可观测量。我们可以使用存在于可观察对象上的`pipe()`函数来实现这一点。从上面取`source`,我们可以创建一个只传递偶数的管道观测值。

```
const source = interval(1000);
source.pipe(
  filter(value => value % 2 === 0)
)
.subscribe(value => console.log(value)) 
```

Enter fullscreen mode Exit fullscreen mode

这将打印出`2 ... 4 ... 6 ... etc`。我们可以看到，可观察对象具有可以作用于发射值的操作符。这里我们使用`filter(select: Function)`只接受偶数的值。这类似于 JavaScript 中数组的`filter()`函数。

我们可以利用大量可管道化的操作符。
假设我们有一个可观察的点击事件来源:

```
const source = fromEvent(document, 'click'); 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个 Observable，每当页面上有一个`click`事件时，Observable 就会发出。

现在，假设我们需要将这些点击事件收集成 5 个一批，然后将它们发送给任意 API 进行处理，然后从处理中返回一个我们需要打印的值，我们将假设我们已经编写了一个准备进行 API 调用的服务，因为这超出了本文的范围。这里我们可以利用`bufferCount(bufferSize: number, startBufferEvery: number = null)`来做这件事。

```
const source = fromEvent(document, 'click');
source.pipe(bufferCount(5)) 
```

Enter fullscreen mode Exit fullscreen mode

`bufferCount(5)`将从`source`收集 5 次发射，然后以阵列形式发射。现在我们已经对事件进行了批处理，我们需要将它们发送给 API。我们的服务将从其服务调用中返回一个可观察对象，因此我们需要从我们的源获取值，并将其传递给一个函数，该函数生成一个新的可观察对象，然后返回新的可观察对象。我们可以使用`mergeMap()`操作符。

```
const source = fromEvent(document, 'click');
source.pipe(
  bufferCount(5),
  mergeMap(events => this.myAPIService.serviceCall(events))
)
.subscribe(processedEvents => console.log(processedEvents)); 
```

Enter fullscreen mode Exit fullscreen mode

在几行 RxJs 操作符和函数中，我们创建了一些奇怪的逻辑，可能需要更多的行来完成承诺。

## 结论

RxJs 是一个非常强大的工具，但是很难掌握，尤其是在处理从多个位置检索数据的大型应用程序时。我希望这篇文章有助于阐明可观测量是如何工作的。欢迎在下面的评论中留下任何评论、问题或担忧。

### 附加资源

当我处理可观测量时，我经常检查。他们有一个操作符列表，里面有操作符的例子和解释。还有一个配方列表，显示了操作人员的实际操作。

[官方文档](https://rxjs.dev/api)也包含有用的信息，包括弹珠图和例子，我们将在下一篇文章中介绍。
# 处理订阅的常用方法

> 原文：<https://dev.to/rxjs/common-approaches-to-handling-subscriptions-1nk7>

当开发人员第一次开始使用 RxJS 时，最大的挑战之一是处理订阅。

RxJS 中的**订阅**处理流的执行流程。当我们创建可观测量时，我们“订阅”它们并开始使用它们。相反，当我们“取消订阅”时，我们做相反的事情来停止相同的执行流。

处理这个可能有点棘手。这篇文章将介绍一些在代码中处理订阅的常见模式。

为了让所有人都能使用这些模式，这篇文章也将是框架不可知的。

这篇文章中用到的例子可以在我的 [Stackblitz 项目](https://stackblitz.com/edit/rxjs-example-handle-susbcriptions)中找到。

我将在这里展示代码，并在最后嵌入一个指向我的 Stackblitz 项目的链接。我鼓励您运行我演练的代码示例，以便更好地理解。

## 内存泄露和你的第一次退订

当我们没有成功地取消订阅一个可观察对象时，我们就造成了一种叫做“内存泄漏”的情况。这是指流启动(使用系统资源)且未停止的任何时间。如果在没有“取消订阅”的情况下启动了足够多的流，就会耗尽大量的系统资源，并显著降低应用程序的速度...*这可不是什么好事*。

一个很好的例子是来自创建操作符`interval`的一个简单的可观察值。考虑下面的代码:

```
import { interval } from 'rxjs';

const observable = interval(1000);
const subscription = observable.subscribe(() => console.log('Hello!')); 
```

Enter fullscreen mode Exit fullscreen mode

所以在这个例子中，我们只是使用了[间隔](https://rxjs.dev/api/index/function/interval)操作符来创建一个写“Hello！”每 1 秒钟向控制台发送一次。当我们调用`subscribe`时，我们说每当流发出一个响应时(在本例中是每 1 秒)，我们打印“Hello！”。

这非常简单，但是这里的挑战是，如果我们不调用`unsubscribe`，这个流将继续运行，直到您结束您的会话或销毁相关组件等。这很容易被忽略，而且对性能很重要。

要解决这种情况，需要一个简单的“退订”。因此，考虑同样的代码，但是添加了一个“取消订阅”调用，如下所示:

```
import { interval } from 'rxjs';

const observable = interval(1000);
const subscription = observable.subscribe(() => console.log('Hello!'));
setTimeout(() => {
  subscription.unsubscribe();
  console.log('unsubscribed');
}, 1000); 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:我们在这里使用一个`setTimeout`只是为了让一秒钟过去，让“hello”显示在控制台上。

现在调用了“unsubscribe ”,执行正确结束，您成功地管理了流。

## 使用取运算符

所以在前面的例子中，订阅是通过直接调用`subscribe`和`unsubscribe`来手动管理的。这种模式很好，但也很容易忘记去做。

一种不太容易出错的方法是使用`take`操作符。当传递给一个 Observable 时，`take`操作符使您能够在流中发出一定数量的信号后结束执行。

考虑下面的代码:

```
import { interval } from 'rxjs';
import { take } from 'rxjs/operators';

const intervalCount = interval(1000);
const takeObservable = intervalCount.pipe(take(2));
takeObservable.subscribe(x => console.log(x)); 
```

Enter fullscreen mode Exit fullscreen mode

当您运行这个命令时，您应该看到以下内容:

```
0
1 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您将相同的代码更改为以下代码会怎么样:

```
import { interval } from 'rxjs';
import { take } from 'rxjs/operators';

const intervalCount = interval(1000);
const takeObservable = intervalCount.pipe(take(10));
takeObservable.subscribe(x => console.log(x)); 
```

Enter fullscreen mode Exit fullscreen mode

当您运行这个程序时，您应该会看到和以前一样的结果，但是计数从 0 变成了 9。

发生了什么事？take 操作符只是控制执行流，因此您传入的数字决定了在完成之前它发出值的次数。这里您不必担心内存泄漏，因为完成会正式停止执行流。

除了`take`操作符之外，还有其他多种方法来实现这一行为。

其中包括以下内容:

*   [takeWhile](https://rxjs.dev/api/operators/takeWhile)
*   [持续到](https://rxjs.dev/api/operators/takeUntil)
*   [takeLast](https://rxjs.dev/api/operators/takeLast)
*   [第一次](https://rxjs.dev/api/operators/first)

这种行为的重要之处在于，您让 RxJS 为您处理流。这让您可以编写易于维护的干净代码。

## 组合订阅

您遇到的另一个常见模式是当您有多个观察对象，并且想要一起管理它们的订阅时。

考虑下面的代码:

```
import { Subscription, of } from 'rxjs';

// create a subscription object
const subs = new Subscription();

// create observables
const value$ = of(1, 2, 3, 4);
const anotherValue$ = of(true);

// subscribe to observables and add to subscription
subs.add(value$.subscribe(x => console.log(x)));
subs.add(anotherValue$.subscribe(x => console.log(x)));

// calling subs.unsubscribe() will unsubscribe from all sub
subs.unsubscribe(); 
```

Enter fullscreen mode Exit fullscreen mode

> 特别感谢我的朋友 Tim Deschryver 的这个例子

在这个例子中，你可以看到我们定义了一个[订阅](https://rxjs.dev/api/index/class/Subscription)的实例，我们向其中添加了两个 observables。`Subscription`类使您能够将您的订阅包装在一个资源中。当您准备好释放您的应用程序时，您可以只调用一个单独的`unsubscribe`,所有被包装的可观察对象的执行都将被正确地停止。

当您想要一起管理一个组件中的多个可观察对象时，这种模式特别有用。这使得实现更清晰，更易于维护。

## 用点击和合并合并订阅

除了上面的例子，另一个常见的模式是利用 [tap](https://rxjs.dev/api/operators/tap) 操作符和静态 [merge](https://rxjs.dev/api/index/function/merge) 函数来合并多个可观测量。

考虑下面的代码:

```
// create observables
const value$ = of(1, 2, 3, 4).pipe(tap(x => console.log(x)));
const anotherValue$ = of(true).pipe(tap(x => console.log(x)));

const subs = merge(value$, anotherValue$).subscribe();

subs.unsubscribe(); 
```

Enter fullscreen mode Exit fullscreen mode

静态的[合并](https://rxjs.dev/api/index/function/merge)功能使您能够将许多可观察值合并成一个值。然后当你准备停止执行时，一个单独的`unsubscribe`就停止了组上的执行。这种模式非常简洁，使 RxJS 能够处理流的编排，而不需要声明额外的操作符等。

## 关闭思绪

所以在这篇文章中，你看到了一些用 RxJS 处理订阅的模式。真正了不起的是 RxJS 非常灵活，可以适应(几乎)任何用例。我希望这里的例子已经为您的应用程序提供了一些基本的模式。欢迎在 Twitter 上留下评论并关注我，地址: [@AndrewEvans0102](https://twitter.com/andrewevans0102) ！

这里有一个上面例子的 stack blitz:
[https://stackblitz.com/edit/rxjs-example-handle-susbcriptions?embed=1&&](https://stackblitz.com/edit/rxjs-example-handle-susbcriptions?embed=1&&)
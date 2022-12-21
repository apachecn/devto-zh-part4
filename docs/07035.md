# RxJS:欢迎加入升级版 ShareReplay 操作员

> 原文：<https://dev.to/2muchcoffeecom/rxjs-welcome-on-board-upgraded-sharereplay-operator-428c>

## 👀背景

你精通 RxJS 吗？这就是我的想法，直到去年 2 月我在 Github 上看到了关于 RxJS shareReplay 功能的公开问题。然而，根据本·莱什的描述，从一开始，就不清楚它在不同场景下的运作有多精确。

## 👂shareReplay 操作员行为

去年已经讨论了很多关于小心使用 shareReplay 操作符的问题。原因可能是什么？毕竟，我们大多数人“知道”如何以及在什么情况下使用它，对吗？

为了提醒您(或者万一您第一次听说它)，让我们考虑下面的代码:

```
const source$ = interval(1000).pipe(
  mapTo('I\'m from the shareReplay subscription'),
  tap(console.log),
  shareReplay(1)
)
const internalSubscription = source$.pipe(
  mapTo('I\'m from the source$ subscription')
).subscribe(console.log);
setTimeout(() => {
  internalSubscription.unsubscribe();
}, 2000); 
```

我打赌你对上面代码的第一个期望和我的一样。正如我们所知，正在进行的 shareReplay 操作符使用 ReplaySubject 和 refCount 变量来计算内部订阅。当 refCount 由于取消订阅而下降到零时，它应该结束订阅，而不是回收 ReplaySubject，对吗？

我的意思是，一旦我们取消订阅源，可观测区间将停止发出新的值...不幸的是，我的期望破灭了，因为 shareReplay 永远不会结束引擎盖下的订阅，并且在我们停止侦听后，interval observable 一直在发出值。

您将看到下一个输出:

```
// 1st emit
I’m from the shareReplay subscription
I’m from the source$ subscription
// 2nd emit
I’m from the shareReplay subscription
I’m from the source$ subscription
// 3rd emit
I’m from the shareReplay subscription
// 4th emit
I’m from the shareReplay subscription
... 
```

你惊讶吗？是的，这完全是意料之外的行为，在特定的场景中，例如无尽的流数据，可能会导致巨大的内存泄漏，甚至是你的应用程序中的一些错误。救援会是什么？🤔

## 💪合并的 PR 和升级的功能

经过社区内部六个月的热烈讨论，决定开放一个 <u>[拉请求](https://github.com/ReactiveX/rxjs/pull/4059)</u> ，建议将配置参数(一个对象)添加到 shareReplay 操作符。这是:

```
export interface ShareReplayConfig {
  bufferSize?: number;
  windowTime?: number;
  refCount: boolean;
  scheduler?: SchedulerLike;
} 
```

更具体地说，refCount 选项可用于控制行为。如果您设置了以下配置 *{ refCount: true }* ，那么对 source$的订阅将是一个引用计数，显然当您取消订阅 *source$* 时， *refCount* 将从 1 变为 0。默认情况下，它不是，您将获得与以前相同的功能。查看下面的代码示例:

```
const source$ = interval(1000).pipe(
  mapTo('I\'m from the shareReplay subscription'),
  tap(console.log),
  shareReplay({bufferSize: 1, refCount: true})
)
const internalSubscription = source$.pipe(
  mapTo('I\'m from the source$ subscription')
).subscribe(console.log);
setTimeout(() => {
  internalSubscription.unsubscribe();
}, 2000);
// When executed, we see in the console:
// 1st emit
I’m from the shareReplay subscription
I’m from the source$ subscription
// 2nd emit
I’m from the shareReplay subscription
I’m from the source$ subscription 
```

最后，PR 于 2019 年 1 月 30 日合并部署在最新版本的 RxJS 6.4.0 内。

## 👌结论

总而言之，如果你第一次听说 shareReplay 问题，并且你可能已经用它来处理无尽的流媒体数据，你现在可能会有不好的感觉，因为你的应用程序可能会遇到麻烦。尤其是当您无法将当前的 RxJS 版本更新到最新版本并将新配置传递给 shareReplay 时。

至少，只要你没有这种可能性，并且希望你的应用程序稳定工作，你就应该选择更安全的选项——使用 publishReplay 和 refCount 来避免可能的内存泄漏。否则，更新你的 RxJS，在必要的地方实现新的配置，最后你就有机会睡个好觉了！

喜欢吗？我们已经尽力了！去我们的 <u>[博客](https://2muchcoffee.com/blog/)</u> 找更多有用的文章。
# RxJS 模式:效率和性能

> 原文：<https://dev.to/gc_psk/rxjs-patterns-efficiency-and-performance-2i6m>

## 所有 RxJS 操作符和技术的概要，你可以利用它们来避免不必要的计算，使你的代码更快更快

RxJS 是一个帮助编写异步或基于回调的代码变得更容易的库，正如在其[主页](https://rxjs.dev/)上所描述的。

但是您知道 RxJS 也可以帮助您的应用程序**更加高效和高性能**吗，这要归功于它强大的操作符。

在这篇文章中，我想分享最有用的运算符和技术，它们有助于避免不必要的重复计算，从而使你的应用程序更快、更有效，我们需要的只是一些 RxJS 魔法。

*注意*:下面的例子是简化的，可能不是完整的片段。

### 提示:通过使用正确的代码共享工具来优化团队合作

使用 [Bit](https://bit.dev?utm_medium=content&utm_source=bitsandpieces&utm_content=16&utm_campaign=teamwork) 在单独的 React 组件上共享、安装和协作。停止浪费时间配置软件包、管理多个存储库或维护繁琐的 monorepos。

[![Components with Bit: Easily share across projects as a team](img/22e7d7d8ecd6df57e5edad03c14a5259.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--XuGB7JRI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AODhvAjHjB8zPErBYhOFePQ.gif) *带 Bit 的组件:轻松作为团队跨项目共享*
[作为团队 Bit 共享可重用代码组件
*轻松作为团队在项目和应用之间共享可重用组件以更快地构建团队。合作开发…*bit.dev](https://bit.dev?utm_medium=content&utm_source=bitsandpieces&utm_content=16&utm_campaign=teamwork)

## 过滤

过滤操作符允许我们从流中过滤出我们想要忽略的事件，并避免将它们发送给可观察对象的订阅者。如果我们在管道中足够快地过滤事件，我们就可以避免将它们传递给其他操作者和订阅回调。

当然，如果管道正在进行繁重的计算或 HTTP 请求，这一点尤其重要。

这些操作符主要用于逻辑而不是性能原因，尽管它们对于避免计算不必要的任务仍然很有用。

当编写一个流时，你可以问自己的问题是:我应该通过所有的项目，还是可以在管道中的某个点跳过它们？

### 滤镜

避免不必要计算的最简单的操作符是 filter。

如果您已经熟悉 Array.prototype.filter 方法，那么您可能已经知道它的用法:我们将一个谓词作为参数传递给操作符，如果它为正在流式传输的事件返回 true，则该事件将通过管道传递，否则将被丢弃。

```
 const numbers$ = of(1, 2, 3, 4, 5);
    const predicate = (n) => n <= 2;

    numbers$
        .pipe(
            filter(predicate)
         )
        .subscribe(console.log);
    // will log 1,2 
```

### 明显改变

另一种类型的筛选运算符是 distinctUntilChanged。

该操作符将当前值与源可观察值的前一个值进行比较，如果两者不同，它将传递该项目。简而言之，它就像过滤器一样工作，但是将比较以前的和当前的值。

为了解释这个操作符是如何工作的，我们可以使用一个非常常见的场景:从一个文本输入接收输入，并跳过其值与前一个值相同的所有事件。

```
 const textChanges$ = fromEvent(textElement, 'input');

    textChanges$
        .pipe(
            filter(Boolean),
            distinctUntilChanged()
        )
        .subscribe(console.log); 
```

但这只对原始值有效。如果您想传递一个更强大的等式谓词，那么您可以提供一个函数并手动比较以前和当前的值。

```
 const stream$ = /* some Rx stream with objects */
    const isEqual = require('lodash/isEqual');

    changes$
        .pipe(
            distinctUntilChanged(isEqual)
        )
        .subscribe(/**/); 
```

## 去抖和节流

去抖动和节流是用于在单次发射中对时间窗口内发射的事件进行批处理的技术。

这两种技术有时可以互换使用，即使它们以不同的方式实现相似的东西。

### 节流时间

操作符 throttleTime 用于仅发出在以毫秒为单位指定的时间窗口内收到的第一个项目，然后在可以发出后续事件之前再次等待整个时间窗口。

```
 const textChanges$ = fromEvent(textElement, 'input');

    textChanges$
        .pipe(
            filter(Boolean),
            distinctUntilChanged(),
            throttleTime(1000)
        )
        .subscribe(console.log); 
```

让我们用一个简单的可视化表示来解释这一点:

```
time in ms : 0---500---1000---1500---2000
events     : _a_____b__________c__d___e__ 
```

发出了哪些事件？a 和 c！第一个事件 a 是在时间范围 0 和 1000 内收集的，第二个事件 b 被跳过，因为它是在窗口内发出的。然后 c 发射，d 和 e 过滤。

### 去抖时间

与 throttleTime 相反，操作符 debounceTime 用于仅发出在以毫秒为单位指定的时间窗口内接收的最新项目。就像节流一样，去抖将在可以发出新事件之前等待时间窗口。

```
 const textChanges$ = fromEvent(textElement, 'input');

    textChanges$
        .pipe(
            filter(Boolean),
            distinctUntilChanged(),
            debounceTime(1000)
        )
        .subscribe(console.log); 
```

让我们重复使用与`throttleTime`相同的表示来理解不同之处:

```
 time in ms : 0---500---1000---1500---2000
    events     : _a_____b__________c__d___e__ 
```

在这种情况下，只有 b 和 e 排放。

### 节流和去抖有什么帮助？

这些操作符用于延迟和批处理一个时间范围内重复事件的执行。

它们在各种情况下都有帮助，我们希望避免无用的命令执行或像 HTTP 请求这样昂贵的操作。

想象一下，用户输入的变化会触发对服务器的请求:如果我们不去抖，我们不仅会发送垃圾服务，还会降低用户的 UX。根据我的经验，每 250 到 500 毫秒去抖一次是确保用户体验流畅的最佳时机。

## 取消

取消订阅是一个很重要但经常被忽视的任务，我在查看 PRs 时经常看到。

取消不仅对减少无用的计算和避免内存泄漏很重要，而且更重要的是，对防止应用程序中可能出现的错误也很重要。

### 退订

取消订阅的最简单、最直接的方法是调用每个订阅对象都应该实现的 unsubscribe 方法。

```
 const inputs$ = fromEvent(element, 'input');
    const subscription = inputs.subscribe(/*some work*/);

    subscription.unsubscribe(); 
```

虽然这是一个非常有效的取消订阅流的工作示例，但通常不被认为是最佳实践。事实上，Rx 提供了强大的操作符，可以帮助我们实现同样的目标，但是以一种更具声明性和反应性的方式。

### 持续到

我最喜欢的退订方式是 takeUntil。当作为输入传递给运算符的流发出事件时，此运算符将允许您取消订阅流。哦，这看起来太复杂了，但实际上并不复杂。

让我们看一个例子:

*   我们有两个分别以 1000 毫秒(1 秒)和 100 毫秒间隔发射的可观测信号

*   每 100ms 发出的流将在另一个流发出时取消订阅，这将每 1 秒发生一次

```
 // emit every 1 second
    const slow$ = interval(1000);

    // emit every 100 ms
    const fast$ = interval(100).pipe(
        takeUntil(slow$)
    );

    fast$.subscribe({
      next(n) {
        console.log(n);
      },
      complete() {
        console.log('I am unsubscribed!');
      }
    });

    slow$.subscribe(); 
```

这将产生以下输出:

```
0
1
2
3
4
5
6
7
8
I am unsubscribed! 
```

### 耗时

该操作符对于根据流本身的值取消订阅流非常有用。我需要使用这个操作符的方法之一是，一旦定时器达到一定的迭代次数，就停止它。例如，倒计时定时器。

在下面的例子中，我想在定时器迭代 5 次后停止它。—

*   takeWhile 操作符接受一个谓词函数，它的参数是流的当前值

*   如果谓词为真，它将不断发出值；如果是 falsy，那么它将取消订阅流

```
 const stream$ = interval(1000).pipe(
      takeWhile(n => n < 5)
    );

    stream$.subscribe({
      next(n) {
        console.log(n)
      },
      complete() {
        console.log('I am unsubscribed!')
      }
    }); 
```

这将产生以下输出:

```
0
1
2
3
4
I am unsubscribed! 
```

### [交换机映射](#switchmap)

算子切换图通常用于展平一系列可观测量。

你可能知道的是，它有一个奇特的行为:在每次发射时，它不是保持一个以上的内在可观测值，而是完成先前的内在可观测值，然后发射新的。

也就是说，如果我们有一个正在运行的 HTTP 请求，当另一个发射发生时，它将被取消。当然，根据你使用的可观察类型，你会有不同的拆卸效果。

在下面的例子中，我创建了一个简单的代码片段，当用户在表单中输入值时，它会发出请求。该请求将在 Github 的 API 中查询存储库，并将它们呈现在屏幕上。

在文章的最后，我们将通过添加一个强大的缓存机制和代码链接来重温这个例子。

## 配料

在非常短的时间内重复执行非常昂贵的操作的情况下，比如对来自流的更新重新呈现 DOM 树，批处理可以帮助收集更新并立即呈现它们。

我第一次使用这种技术是在使用 Angular.js 的时候:每次从后端进行更新时，digest 循环都被调用了很多次，它为应用程序计时。

那时我想:为什么不在一个数组中批量更新，然后每 1 或 2 秒更新一次呢？为了做到这一点，我们可以使用 buffer 或 bufferTime 操作符(或者缓冲系列中的其他操作符)。

### 缓冲时间

operator bufferTime 是 operator buffer 的快捷方式，它接受以毫秒为单位的时间量，并将在数组中每隔 n 毫秒对流进行批处理。

例如，在下面的例子中，我们模拟了一个每 500 毫秒发出一次的流。缓冲时间将设置为 2 秒。这意味着，我们在 2 秒钟的窗口内收集了 4 次更新。

```
 this.stream$ = interval(500);

    this.data$ = this.stream$.pipe(
      bufferTime(2000),
      filter((items) => items.length > 0),
      scan((acc, items) => {
        return [...acc, ...items];
      }, [])
    );

    this.data$.subscribe((items) => {
      /* expensive operation here */

      console.log('re-render!');
    }); 
```

让我们总结一下上面的内容:

*   我们有一个用数据发出事件的流(在这个例子中，它只是一个用数字表示的区间)

*   我们用 bufferTime(2000)每 2 秒对事件进行批处理

*   我们过滤所有空的事件；在我们的例子中没有什么意义，因为我们总是有值，但这可能是您在实际应用程序中想要做的事情，因为有时您会收到不需要重新呈现的数据

*   我们使用 scan 操作符，其工作方式就像 Array.prototype.reduce 一样。我们收集事件并将它们全部分组到一个数组中——以防我们处理一个想要重新呈现的大列表

使用这种技术时，有两件事需要注意:

*   测试性能！缓冲将在收集事件时继续工作:只有在渲染或其他昂贵的操作成为应用程序的真正瓶颈时才使用它

*   由于上述原因，不需要的时候记得取消订阅

## 缓存

每个应用程序都需要某种程度的缓存来提高效率和性能。缓存很重要的一个最常见的情况是 HTTP 请求，我们可以利用 RxJS 很容易地做到这一点。

例如，我们可能不仅想要返回缓存的响应，还可能想要停止任何返回相同值的正在进行的请求，当然假设请求是等幂的。

在下面的例子中，我们将使用缓存来扩展我们的存储库搜索应用程序。缓存以这种方式工作:

*   我们创建一个映射来存储请求的名称和结果

*   当我们要发出请求时，我们检查是否有缓存的结果。如果没有，我们继续执行请求

*   当执行请求时，我们将可观察对象本身放在缓存中，并使用操作符 shareReplay(1)将最近的 1 次发射存储在内存中。当可观察对象(在缓存中)再次被订阅时，它将产生结果，而不是再次发出请求

上面的例子是简化的，没有考虑错误处理等。因此，如果您将它作为代码的灵感，您可能想要处理更多的情况。

如果你想看实际操作，请访问下面的 [Stackblitz 链接](https://stackblitz.com/edit/switchmap-with-cache-example?file=index.ts)。

## 外卖

RxJS 是一个非常强大的工具。虽然一开始它的学习曲线可能很陡，但是学习如何使用它是一项很好的技能，可以添加到您的个人开发人员库中。

虽然它可以轻松地使您的代码更具声明性和可读性，但它也可以帮助我们提高应用程序的效率和性能，只需在我们的流中添加几行代码。

上面的技术是一个详尽的，但远非完整的技术列表，它使用 RxJS 操作符来提高代码的效率和性能。

如果您需要任何澄清，或者如果您认为有些事情不清楚或错误，请留下评论！随时欢迎反馈。

我希望你喜欢这篇文章！如果你有，请在 [媒体](https://medium.com/@.gc?source=post_page---------------------------)、[推特](https://twitter.com/home?source=post_page---------------------------)或我的[网站](https://frontend.consulting/)上关注我，获取更多关于软件开发、前端、RxJS、Typescript 等方面的文章！*
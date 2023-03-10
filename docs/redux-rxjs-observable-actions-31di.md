# Redux + RxJs:可观察的动作

> 原文：<https://dev.to/lysofdev/redux-rxjs-observable-actions-31di>

RxJs 是我最喜欢的 Javascript 库之一。这几乎是对语言本身的更新。在最近的一个角色中，我们使用 RxJs 为用户设备中的多个大型数据集构建了一个出色的数据管道，用于处理排序、过滤、聚合和分页。我们还使用了一个 [Redux](https://redux.js.org/) 存储来维护应用程序的状态，并使用 [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) 来存储用户设备中的大型数据集。有了这个和其他一些技巧，我们能够交付一个离线工作的渐进式 web 应用程序。

在这篇文章中，我想和你分享一个定制的 Redux 中间件函数，我们开发这个函数是为了轻松处理 Redux 动作中的[可观察对象](http://reactivex.io/documentation/observable.html)。在 [NPM](https://www.npmjs.com/) 有几个图书馆宣传有这样的能力，但是我们发现解决方案很简单，对第三方图书馆进行安全审计只是浪费时间。

我们希望通过将所有业务逻辑排除在外来保持我们的 [React](https://reactjs.org/) 视图层的纯净。应用程序呈现 Redux 存储的状态，其中包括从 IndexedDB 中提取可见记录所需的查询。用户的任何动作都被映射到发送到商店的派单，这可能是一个简单的动作，也可能是一个可观察到的动作。我们跳过了接受函数作为 thunk 动作，因为这基本上与可观察的动作是一回事，但是中间件实际上也忽略了这些，所以与 [Redux-Thunk](https://github.com/reduxjs/redux-thunk) 结合是安全的。

我们确定存在两种类型的逻辑，我们希望存储在可观测量中。

*   商业
*   异步的

这才是真正的 RxJs。我们没有使用复杂的或几个中间件、中间动作和复杂的 reducers 来处理网络请求和其他业务任务，而是将所有这些都委托给 RxJs，它允许我们隔离逻辑并无缝地组合它。Redux 存储侧重于将动作映射到状态，并通知 React 应用程序状态的变化。使用 RxJs，我们可以将用户调度的动作映射到 Redux store 的写操作，所有这些都在一个可观察的接口中，该接口无缝地隐藏了异步调度和几个中间任务。

好了，说够了。下面是一个工作示例:

[https://repl.it/@EstebanHernande/LargeLuxuriousNumerators?lite=true](https://repl.it/@EstebanHernande/LargeLuxuriousNumerators?lite=true)

上面是一个简单的脚本，它使用 Redux 存储将数字相加并返回总和。它还跟踪错误，并有一个加载标志，使用户不会收到中间值。

如果你看一下`counterReducer`，我们需要记住四种行动类型。`_RESET`动作类型是同步发出的，所以 reducer 将在任何来自可观察对象的通知之前收到这个通知。这可用于重置某些状态，如清除错误、清除计数器以及将装载平台设置为`true`。

`observerMiddleware`将处理可观察的动作。

默认情况下，一旦遇到承诺或任何其他类型的异步操作，可观察对象将尝试同步执行并切换到异步处理。这可能会导致在`_RESET`之前发出`_NEXT`、`_ERROR`和`_COMPLETE`通知，这可能会导致`_RESET`通知在我们更新后清除状态。我们需要改变可观察对象上的默认[调度器](http://reactivex.io/documentation/scheduler.html)来防止这种情况。幸运的是，RxJs 提供了一种非常简单的方法来保证可观察对象被异步处理。我们所要做的就是将`observeOn` [操作符](http://reactivex.io/documentation/operators.html)和`asapScheduler`应用到可观察对象上，然后就完成了！

现在，我们的可观察对象将在`_RESET`通知后开始处理，并将每个值作为`_NEXT`通知发出。我们的 reducer 将为每个通知更新计数器的状态。

最后，如果可观察对象发出了一个`_ERROR`通知，我们的 reducer 将会用错误更新状态。否则，可观察对象将发出一个`_COMPLETE`通知，我们的 reducer 将处理该通知以将加载标志设置为`false`。现在，我们面向用户的应用程序可以删除加载指示器并显示总和(或错误消息)。

我们将通过创建一个名为`addNumbers`的动作生成器来测试这一点，该生成器接收任意数量的数字作为参数，并调度一个带有可观察有效负载的动作，该有效负载在完成之前按顺序发出这些数字。给定数字 1 到 4，我们期望总和为 10。如果我们运行上面的脚本，我们可以看到一旦装载平台被设置为`false`，并且`counter`的值将被设置，通知我们该进程已经完成装载，并且总和为`10`。
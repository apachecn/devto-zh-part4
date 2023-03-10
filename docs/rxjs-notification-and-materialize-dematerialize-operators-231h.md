# RxJS 通知和物化/去物化操作符

> 原文：<https://dev.to/wojciechmatuszewski/rxjs-notification-and-materialize-dematerialize-operators-231h>

> 本文假设读者对 RxJS 有基本的了解。如果你不熟悉 RxJS，我强烈推荐[通读文档](https://rxjs-dev.firebaseapp.com/)。

最近，在浏览 [RxJS 文档](https://rxjs-dev.firebaseapp.com/)寻找如何解决我所面临的一个问题的灵感时，我注意到了一个我从未使用过的类型(以及随之而来的操作符):`Notification`。

今天，我想分享我在摆弄它时学到的东西。

# 通知

[文件](https://rxjs-dev.firebaseapp.com/)声明`Notification`是:

> [...]可观察对象可以发出的基于推送的事件或值。...除了包装实际交付的值，它还用元数据对其进行注释

让我们试着一点一点地分解它。

## 可观察到的事件或值可以发出

`Observable`能发出*

*   不完全的
*   不良事件
*   下一个

让我们尝试使用`Notification` API 来复制这种行为。

```
import { Notification, from } from "rxjs";

// Notification(Type, Value?, Error?)

const onErrorNotification = new Notification("E",{}, 'onError' );
const onCompleteNotification = new Notification("C", 'onComplete');
const onNextNotification = new Notification("N", 'onNext');

from([onNextNotification, onCompleteNotification, onErrorNotification])
  .subscribe(console.log);

/* {
 *  kind: 'E' or 'C' or 'N'
 *  value: 'onNext' or 'onComplete' or undefined
 *  error: undefined or 'onError'
 *  hasValue: true or false (onNext hasValue is always true)
 * }
 */ 
```

Enter fullscreen mode Exit fullscreen mode

我没有使用`onComplete`和`onError`处理程序来记录值，为什么？

这是因为`Notifications`被视为`onNext`事件，但它们(本例中的**)代表底层事件和值，所以`onComplete`和`onError`处理程序永远不会触发。**

 **## 用元数据标注(实际值)

`Notification`承载了大量的信息:

*   它告诉你事件的类型(用`kind`道具)
*   它向您展示了实际值(使用`value` prop)
*   它会通知您错误(使用`error` prop)及其值
*   它告诉你事件是否是基于*值的* ( `onNext`)

这一切都很棒，但是我们如何将`Observable`事件转换成`Notification`？

输入:`materialize`和`dematerialize`

# `materialize`和`dematerialize`

这些运算符非常有趣。
它们允许你控制你当前居住在事件或价值的哪个‘领域’(`Notification`或正常，`Observable`)。

## `dematerialize`

该操作符允许您将`Notification`降级到给定`Notification`的底层`Observable`事件或值。

让我们重新编写第一个代码示例，这样我们实际上必须连接所有 3 个处理程序(`onNext`、`onError`、`onComplete`)来获取所有数据。

```
from([ onNextNotification, onErrorNotification, onCompleteNotification ])
  // 'degrading' to an Observable-event world
  .pipe(dematerialize())
  .subscribe({
    next: console.log,
    error: console.log,
    complete: console.log
  });

/* onNext
 * onError
 */ 
```

Enter fullscreen mode Exit fullscreen mode

为什么没有注销`onComplete`？

> 任何给定的流只能出错一次。这是由可观察契约定义的，它说一个流可以发出零个或多个值。

在我们的例子中，这意味着流已经错误地结束了它的生命周期，并且不会发出任何进一步的值。

这种情况暗示了这样一种用例，即尽管有错误，我们仍想继续我们的操作链。

## `materialize`

就像，`dematerialize`“降级”事件，`materialize`，使您能够将给定事件“升级”为`Notification`类型。

假设我们知道我们的 source `Observable`可以随机抛出，但是我们仍然想要遍历我们的操作符链。

```
import { dematerialize, materialize, map, delay } from "rxjs/operators";
import { Notification, timer } from "rxjs";

sourceThatThrows$
  .pipe(
    // Promoting the event to a Notification
    materialize(),
    map(notification =>
      // Was the event value based? (onNext, onComplete)
      notification.hasValue
        ? notification
        // As alternative you could also use new Notification(...)
        : Notification.createNext("was error value!")
    ),
    delay(100),
    dematerialize()
  )
  .subscribe(console.log);

/* value from source
 * was error value
 */ 
```

Enter fullscreen mode Exit fullscreen mode

使用、`materialize`和`dematerialize`操作符，我们成功地保留了我们的操作符链，即使源可以随机抛出。

# 总结

RxJS 生态系统非常庞大，有 100 多个运营商，有很多东西需要学习。我希望，我能够对这两个问题提供一些基本的知识。

可以在推特上关注我 [@wm_matuszewski](https://twitter.com/wm_matuszewski)

谢谢👋

# 脚注

*我不是专家，可能有更多的东西可以观察到。为了这篇文章，我假设了这三个事件。

编辑:

*   感谢 [Christopher Hiller](https://dev.to/boneskull) 指出`Notification`作为`onComplete`事件将`hasValue`设置为 false，因此它不是*基于值的*事件。**
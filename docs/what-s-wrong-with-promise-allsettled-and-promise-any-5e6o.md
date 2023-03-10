# Promise.allSettled()和 Promise.any()❓怎么了

> 原文：<https://dev.to/vitalets/what-s-wrong-with-promise-allsettled-and-promise-any-5e6o>

我最近在 v8 博客上读了[承诺组合子](https://v8.dev/features/promise-combinators)的文章。是关于 Promise API 即将推出的两个方法:`Promise.allSettled()`和`Promise.any()`。我感到沮丧。这些方法的设计在我看来与当前的 Promise API 不一致。下面我来分享一下我的看法。

# 承诺

根据文章:

> 当所有输入的承诺都完成时，给你一个信号，这意味着它们要么完成，要么被拒绝。

用例是发送几个 API 调用并等待全部完成:

```
const promises = [
  fetch('/api-call-1'),
  fetch('/api-call-2'),
  fetch('/api-call-3'),
];

await Promise.allSettled(promises);

removeLoadingIndicator(); 
```

Enter fullscreen mode Exit fullscreen mode

当然，这是有用的。但是这个任务用`.map()`和`Promise.all()`就能轻松解决。变化很小:

```
const promises = [
  fetch('/api-call-1'),
  fetch('/api-call-2'),
  fetch('/api-call-3'),
].map(p => p.catch(e => e)); // <-- the only change

await Promise.all(promises);

removeLoadingIndicator(); 
```

Enter fullscreen mode Exit fullscreen mode

实现一个只需几行代码就能解决的新核心方法值得吗？对我来说，这是一个库的特性，而不是核心的 API 方法。

但更重要的是`Promise.allSettled`带来了额外的抽象，增加了代码复杂度。与`Promise.all`不同，它通过包装对象的数组`{status, reason}`来实现，而不是纯粹的承诺值。作为开发商，我不喜欢这样。我认为名字相似的方法`.all()/.allSettled()`行为相似。但是他们没有。

此外，带有`Promise.allSettled`的代码鼓励更差的错误控制。错误应该从最终结果中过滤掉，而不是传统地在 catch 块中处理。这反过来又有以下缺点:

*   错误发生时，不会立即处理。在几个相关错误的情况下，你无法知道哪一个是原始的。并且日志将包含不正确的时间戳。
*   如果至少有一个承诺永远待定，则不会处理错误。

当前`Promise.all`的方法不允许这样的事情。

# 许诺

> 一旦承诺兑现，就给你一个信号。

换句话说，`Promise.any`是忽略拒绝的`Promise.race`。

用例是检查几个端点，并从第一个成功的端点获取数据:

```
const promises = [
  fetch('/endpoint-a').then(() => 'a'),
  fetch('/endpoint-b').then(() => 'b'),
  fetch('/endpoint-c').then(() => 'c'),
];
try {
  const first = await Promise.any(promises);
} catch (error) {
  // All of the promises were rejected.
  console.log(error);
} 
```

Enter fullscreen mode Exit fullscreen mode

我同意有时它可能是有用的。但是多久一次呢？你在多少个项目中使用了模式*对相同的数据*向相同的端点发出几个并行请求？欢迎在评论中分享。但在我看来-不经常。让社区获得[蓝鸟的](http://bluebirdjs.com) `Promise.each()`或`Promise.delay()`的本地实现难道不是更有用吗？

而且，`Promise.any`引入了一种新的误差类型——`AggregateError`。如果所有承诺都被拒绝，此类错误包含到其他错误的链接。又一个错误处理方法！它不同于从成功结果中提取错误的`Promise.allSettled`。它也不同于`Promise.all/Promise.race`只用一个`Error`实例拒绝。如果每一个新的 Promise API 方法都引入了新的错误处理方式，JavaScript 会是什么样子？虽然这个提议还处于非常早期的阶段，但我很关心它的方向。

基于当前的 Promise API，`Promise.any`的实现有点棘手，但实际上[两行代码](https://github.com/m0ppers/promise-any/blob/master/index.js) :

```
const reverse = p => new Promise((resolve, reject) => Promise.resolve(p).then(reject, resolve));
Promise.any = arr => reverse(Promise.all(arr.map(reverse))); 
```

Enter fullscreen mode Exit fullscreen mode

难道我们不应该把它留在图书馆的土地上，保持核心承诺 API 的干净和简单吗？

# 不一致

为什么`Promise.all`和`Promise.race`这么好看？

因为它们的行为非常一致，与通常的承诺相似:用一个值来履行，用一个错误来拒绝。没有包装的值，没有累积的错误，没有额外的复杂性。

为什么`Promise.allSettled`和`Promise.any`对我来说那么诡异？

*   用状态和原因包装潜在承诺值的对象数组来实现。拒绝……从不。
*   `Promise.any`满足单值，忽略中间拒绝。只有当所有的承诺都被拒绝时，它才会用包含所有潜在原因的累积原因来拒绝。

这些新方法真的很难让我理解。因为它们与当前的 Promise API 非常不同。

我期待 2020 年一个热门的求职面试问题:
*这四种方法有什么区别？*

1.  `Promise.all()`
2.  `Promise.allSettled()`
3.  `Promise.race()`
4.  `Promise.any()`

虽然这是个很酷的问题，但我不认为核心 API 应该鼓励这样的复杂性。

# 命名

我也对命名很失望。行为略有不同的四个方法应该有非常清晰的名称。否则，每次我在代码中遇到他们时，我都必须重新检查 [MDN](https://developer.mozilla.org/en-US/) 。来自`Promise.any`的[提案](https://github.com/tc39/proposal-promise-any):

> 它清楚地描述了它的功能

让我来表示不同意。对我来说,`Promise.any`的名字令人困惑:

*   如果**的任何**承诺兑现，它会兑现吗？**是的。**
*   如果任何**个承诺被拒绝，它会拒绝吗？**号****
*   如果**的任何**承诺达成和解，它会和解吗？**看情况。**
*   与`Promise.race`有何不同？**嗯..**

我认为，每个方法的名字应该明确定义方法实现的条件。我建议采用以下命名约定:

```
Promise.all        -> Promise.allFulfilled
Promise.allSettled -> Promise.allSettled
Promise.race       -> Promise.oneSettled
Promise.any        -> Promise.oneFulfilled 
```

Enter fullscreen mode Exit fullscreen mode

它反映了承诺状态的四种可能组合。它解释了为什么这些方法在提案中被称为*组合子*。
当然，这样的重命名是不可能的，因为`Promise.all`和`Promise.race`已经登陆并在许多应用中使用。但是对于新方法来说，有一些命名策略会非常有帮助。

我已经在 GitHub 的`Promise.any()`提案库中[开了一期](https://github.com/tc39/proposal-promise-any/issues/34)，欢迎大家分享想法。

# 吞下拒绝物

总的来说，我对新方法中引入的非投掷式“吞咽式”拒绝的概念不感兴趣。事实上，新的 Promise API 提供了一种方式来**悄悄地忽略代码**中的错误:

*   `Promise.allSettled`永不拒绝。
*   只有当*所有*的承诺都被拒绝时`Promise.any`才会拒绝。

目前没有其他核心 JavaScript API 能做到这一点。忽略一个错误的唯一方法——手动用空体将其包装到`try..catch / .catch()`中。并且在这里写一个注释为什么你忽略错误，否则 [eslint 会警告你](https://eslint.org/docs/rules/no-empty)。

我认为**核心 API 应该暴露所有错误**。是否处理错误总是由开发人员决定。对于其他开发者来说应该是显式的。想象一下，由于吞咽拒绝的不准确使用，将花费多少小时的调试时间！尤其是在处理第三方代码时——当某些东西不工作并且没有抛出错误时。

# 结论

我每个工作日都用承诺。和许多其他开发人员一样。我喜欢 JavaScript 的异步特性。有了清晰直观的 API，我可以更快地解决任务，提高工作效率。这就是为什么我认为 Promise API 要非常慎重的对待和改变。
感谢阅读，欢迎评论。

*这篇文章最早出现在[hackernoon.com](https://hackernoon.com/whats-wrong-with-promiseallsettled-and-promiseany-yfib64aiv)。*
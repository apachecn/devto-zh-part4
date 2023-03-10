# 你开始担心了吗？

> 原文：<https://dev.to/westbrook/is-the-suspense-getting-to-you-2k5m>

> 免责声明:虽然本文中讨论的代码只有短短的几行代码，但它有一个非常健壮的特性集，其中大部分我并不打算完全理解。虽然我写这篇文章的目的之一是更好地理解这里讨论的代码，我觉得我已经实现了这个目的(许多关于建议的修改的段落已经经历了许多版本)，但我期待您分享任何关于我可能遗漏的功能或误解的见解，以支持对所交付内容的更深入和更广泛的理解。如果你有兴趣分享，请在下面的评论中分享，这样我可以根据需要更新文章的主体。谢谢！

* * *

# 就这样，开始了...

在去年的 Chrome Dev 峰会上， [Justin Fagnani](https://twitter.com/justinfagnani) 展示了对当前可用的 [`lit-html`](https://lit-html.polymer-project.org/) 和 [`LitElement`](https://lit-element.polymer-project.org/) 功能集的一些令人兴奋的扩展。包括对长时间运行的任务进行高级调度、分块渲染等想法，非常值得反复观看。如果你还没有看过，我建议你直接跳到异步渲染的相关工作，由`lit-html`的`runAsync`指令驱动:

[https://www.youtube.com/embed/ypPRdtjGooc?start=1014](https://www.youtube.com/embed/ypPRdtjGooc?start=1014)

`runAsync`看起来像是在使用`lit-html`时可以选择使用的一组[指令](https://lit-html.polymer-project.org/guide/template-reference#built-in-directives)的一个非常棒的补充，虽然我花了很长时间才把它写成文字，但我一直在想这种技术在更声明性的上下文中会是什么样子，更 DOM 驱动的东西。我想利用这一新增功能赋予`lit-html`的能力，并将其应用于`LitElement`，以便更广泛的 web 组件社区可以轻松访问它。类似于:

```
 <do-something-lazily wait="2000">
        <div slot="success">Success</div>
        <div slot="initial">Initial</div>
        <div slot="error">Error</div>
        <div slot="pending">Pending</div>
    </do-something-lazily> 
```

然后，您可以将事情推进一点，这样您就可以通过以下方式获得一个分阶段的“挂起”状态:

```
 <do-something-lazily wait="2000">
        <div slot="success">Success</div>
        <div slot="initial">Initial</div>
        <div slot="error">Error</div>
        <staged-pending slot="pending" wait="500">
            <div slot="success">Waiting a lot</div>
            <div slot="pending">Waiting a little</div>
        </staged-pending>
    </do-something-lazily> 
```

<figcaption>为特定目的而命名的元素，而不是为实际用途而命名的。</figcaption>

# 但是，真的，就这么办吧！

为了实现这一点，我们可以在最具创意的命名为`<run-async />`的元素中应用`runAsync()`指令，如下所示:

[https://stackblitz.com/edit/lit-element-run-async?embed=1&&file=demo-el.js](https://stackblitz.com/edit/lit-element-run-async?embed=1&&file=demo-el.js)

使异步动作的每个可能状态的 DOM 成为以当前阶段命名的槽(尽管`error`似乎比`failure`更合适，我改变主意了！)意味着只需做很少的工作，我们就可以得到上面列出的示例的通用版本。我们可以利用我们的`fallbackAction`,它将`wait`属性翻译成毫秒，在我们的“异步动作”完成之前开始倒计时。通过提供一个真实的`action`，这真的开始变得生动起来。

下面的例子利用了[jsonplaceholder.typicode.com](https://jsonplaceholder.typicode.com/)和管道上的一点点合成延迟，让你真正了解这是如何工作的:

[https://glitch.com/embed/#!/embed/honorable-link?previewSize=100&path=preview](https://glitch.com/embed/#!/embed/honorable-link?previewSize=100&path=preview)

仅作为示例，请注意使用以下内容来帮助占位符 JSON 获得随机数量的额外时间，以将其返回给客户端:

```
 var wait = ms => new Promise((r, j)=>setTimeout(r, ms));

    // ...

    const simulatedDelay = Math.floor(Math.random() * Math.floor(2000));
    await wait(simulatedDelay); 
```

这意味着对内容请求的“即时”响应需要相当长的时间，我们可以体验到“待定”槽中嵌套的`<run-async />`元素的好处。允许 UI 根据网络条件与用户进行更多的交流是这种技术最直接的有价值的好处之一。这比在应用程序获取用户想要交互的内容和数据时，让用户对应用程序中发生的事情保持悬念要好得多。

# 为什么我现在不能拥有？

为什么？确实如此。目前，这个功能正作为一个[公开公关](https://github.com/Polymer/lit-html/pull/657)搁置在 [`lit-html`回购](https://github.com/polymer/lit-html)中，已经有一段时间没有得到[贾斯汀和团队](https://twitter.com/polymer)的喜爱了。*也许*如果每个阅读这篇文章的人都对这个功能感兴趣，我们可以说服他们完成这项工作，并在产品发布中提供它！如果可以的话，我会很高兴用一些测试来包装这个`<run-async />`元素的实现，并让它在 NPM 上排序。

这并不是说当前的实现(指令和我的元素)没有问题。

## 遗留问题和未决问题

### 什么时候初始化？

按照目前的提议，代码进入“初始”状态的唯一方式是抛出一个`new InitialStateError();`，这不是我最喜欢的事情。首先，我认为默认情况下代码应该处于“初始”状态，而不是通过显式动作，所以我不知道为什么我们一开始就需要这个接口(原谅我的双关语)。幸运的是，在我们的`<run-async />`元素的上下文中，我们可以隐藏一些实现细节。然而，这仍然感觉有点可疑，无论是我，你，还是下一个测试`runAsync()`的人，我认为这将继续是人们产生困惑的一个问题。请在下面的评论中分享你对这种方法的想法，*或*更好的是直接评论到 PR 中。您可以[同意我的观点](https://github.com/Polymer/lit-html/pull/657/files#r255359797)，可能提出更好的方法，或者建议一些文档来支持对这个用例的更广泛的理解，以帮助我和下一个人减少困惑。无论哪种方式，我都会把它算作一个胜利！

**当抛出不是真的抛出**
甚至`runAsync()`的代码内部也依靠抛出的错误来管理各种状态转换。特别是，这种方法被用来拒绝我们的懒惰行为，以支持一种新的行为。这里，`runAsync()`内部存储的`pendingPromise`也被抛出拒绝。在为这种可能性做准备时，您将能够获得一个引用，该引用承诺当它通过一个自定义事件移动到“pending”时，此时您可以捕获它遇到任何错误:

```
 this.addEventListener('pending-state', ({detail}) => {
        detail.promise.catch(() => {});
    }); 
```

在上面的例子中，我捕捉了所有可能拒绝这个承诺的东西。如果这是`runAsync()`发布的路径，那么需要为`<run-async />`添加一个 API 来完全管理这个状态。工作永远不会结束，阿米利特？当同一个`pendingPromise`被用来宣布动作的状态从“未决”变为“初始”时，就会产生问题。

```
 Promise.resolve(promise).then(
        (value: unknown) => {
            // ...
        },
        (error: Error) => {
            const currentRunState = runs.get(part);
            runState.rejectPending(new Error()); 
```

这使得`pendingPromise`拒绝，即使当`new InitialStateError()`被抛出时。并且，提供`pendingPromise`的自定义事件仅在`currentRunState.state === 'pending'`时被调度，该事件在微任务之后被查询以反映最近呈现的状态，当您的操作由于缺少键而无法完成时，当立即抛出`new InitialStateError()`时，该事件将是“初始”的。

```
 (async () => {
        // Wait a microtask for the initial render of the Part to complete
        await 0;
        const currentRunState = runs.get(part);
        if (currentRunState === runState && currentRunState.state === 'pending') {
            part.startNode.parentNode!.dispatchEvent(
                new CustomEvent('pending-state', {
                    composed: true,
                    bubbles: true,
                    detail: {promise: pendingPromise}
                })
            );
        }
    })(); 
```

这意味着你还没有收到对`pendingPromise`的引用，它在这个上下文中拒绝引用是为了捕捉抛出的错误。这不会阻碍任何后续的功能，但是让随机错误在应用程序中四处乱飞肯定不是我们工程师引以为豪的事情。为了解决这个问题，我建议我们扩展将调度`pending-state`事件的上下文，以包括“初始”状态，如下所示:

```
 if (
        currentRunState === runState &&
        (
            currentRunState.state === 'pending' ||
            currentRunState.state === 'initial'
        )
    ) { 
```

我不能 100%确定这是否包含了`pendingPromise`应该作为基础的全部功能，但是它允许页面无错误地运行，同时提供了`runAsync()`专门提供的延迟加载的内容 UI。我已经[在 PR](https://github.com/Polymer/lit-html/pull/657/files#r309194878) 中建议了这一变化，所以请随意同意或建议其他的前进道路。

### 我又要“待定”了...

如果您仔细查看了我的代码示例和 PR，您会注意到，在测试是否允许 UI 更新到“pending”状态(当该状态的模板可用时)时，我从`runs`映射中重新获取了`runState`。目前，公共关系概述了以下准则:

```
// If the promise has not yet resolved, set/update the defaultContent
if ((currentRunState === undefined || currentRunState.state === 'pending') &&
      typeof pending === 'function') {
    part.setValue(pending());
} 
```

然而，`currentRunState`在稍后通过以下方式设置为“待定”之前，从之前的运行中提取为`const currentRunState = runs.get(part);`:

```
const runState: AsyncRunState = {
  key,
  promise,
  state: 'pending',
  abortController,
  resolvePending,
  rejectPending,
};
runs.set(part, runState); 
```

这意味着如果你试图给指令提供一个新的`key`，那么`currentRunState.state === 'pending'`的检查永远不会为真。在上面的例子中，这意味着当请求显示第二种(或更晚)形式的数据时，您将无法返回到“提前等待”或“长时间等待”消息。

我概述了以下内容来解决这个问题:

```
const runState = runs.get(part);
// If the promise has not yet resolved, set/update the defaultContent
if ((runState === undefined || runState.state === 'pending') &&
    typeof pending === 'function') { 
```

虽然我同意这不是最有创意的或者甚至是信息性的变量命名，但是如果不回到当前状态的`runs`图，你将永远无法找到那个状态是`'pending'`。希望[这个建议](https://github.com/Polymer/lit-html/pull/657/files#r307304583)有助于这个附加物很快走向合并。

### TODOs

除了我在准备`<run-async />`时遇到的现实之外，Justin 还提到了一些他希望在这份公关材料中添加润色的具体内容:[在这里](https://github.com/Polymer/lit-html/pull/657/files#diff-47605b25f29d88148b59b1949601eb08R59)和[在这里](https://github.com/Polymer/lit-html/pull/657/files#diff-47605b25f29d88148b59b1949601eb08R66)。定制`key`失效的能力和中止承诺时定制错误的发射肯定会是对这一功能的质量补充。然而，我觉得他们不需要以任何方式阻止公关。扩展这个指令来支持这些附加特性，看起来是在尽快发布和确保长期覆盖所有用例之间的一个不错的平衡。

# 你怎么看？

你对`runAsync()`指令有什么看法？将这样的东西包装在自定义元素中有意义吗？我是否以一种你可以从中获益的方式包装了指令？我很想在下面的评论中听到你的想法。我也可以在 Twitter 上以 [@westbrookj](https://twitter.com/WestbrookJ) 或者在 [Polymer Slack 频道](https://join.slack.com/t/polymer/shared_invite/enQtNTAzNzg3NjU4ODM4LTkzZGVlOGIxMmNiMjMzZDM1YzYyMzdiYTk0YjQyOWZhZTMwN2RlNjM5ZDFmZjMxZWRjMWViMDA1MjNiYWFhZWM)找到。如果你想谈论更多关于懒惰的用户界面、文学元素、现代网络或即兴音乐，请到这些地方来找我...我总是下来聊天！

* * *

```
 <do-something-lazily wait=${justinMergesThePRInMilliseconds}>
        <div slot="success">Publish to NPM</div>
        <div slot="initial">Have the idea...</div>
        <div slot="error">Learn about issues I've not seen, yet.</div>
        <div slot="pending">Review the PR</div> <!-- WE ARE HERE! -->
    </do-something-lazily> 
```

* * *

在 [Unsplash](https://unsplash.com) 上由 [Tertia van Rensburg](https://unsplash.com/@tertia) 拍摄的封面图片
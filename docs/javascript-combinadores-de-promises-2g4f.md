# JavaScript -组合承诺

> 原文：<https://dev.to/oieduardorabelo/javascript-combinadores-de-promises-2g4f>

## 了解承诺 API 及其消息

自 2015 年推出**承诺**以来，JavaScript 恰好支持两个组合器:静态方法`Promise.all`和`Promise.race`。

目前有两项新建议正在通过标准化进程:`Promise.allSettled`和`Promise.any`。加上这些新增内容，JavaScript 中总共会有**4 个 Promises** 组合器，每一个都允许不同的使用案例。

下面是四个组合体的概述:

*   **:没有短路，[在命题](https://github.com/tc39/proposal-promise-allSettled)**
***   **Promise.all** :输入值被拒绝时短路，添加的不是 2015□*   **Promise.race** :决定(拒绝或解决)任何输入值时的短路，添加的不是 2015*   **Promise.any** :解决输入值时短路，[在命题](https://github.com/tc39/proposal-promise-any)**

 **让我们看一下每个组合体的一个用例示例。

# 许诺一切

[![](img/e3e1289edb8817802c1d97bdf96ff5ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L-7ymEkd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u9uj9tg5cx5of4l4tdmu.png) 
*参见[https://V8 . dev/features/support](https://v8.dev/features/support)*上的支持

`Promise.all`让你知道所有入境承诺何时兑现或其中一项被拒绝。

假设用户单击一个按钮并希望加载一些样式表，以便可以呈现全新的用户界面。此程序以并行方式为每个样式表启动 HTTP 请求:

```
const promises = [
  fetch('/component-a.css'),
  fetch('/component-b.css'),
  fetch('/component-c.css'),
];
try {
  const styleResponses = await Promise.all(promises);
  enableStyles(styleResponses);
  renderNewUi();
} catch (reason) {
  displayError(reason);
} 
```

只有在所有请求都成功后，才能开始渲染新用户界面。如果出了问题，您希望尽快显示错误消息，而不必等待其他作业完成。

在这种情况下，你可以使用`Promise.all`:你想知道什么时候所有的承诺都兑现了，或者一个承诺被拒绝了。

# 无极.竞

[![](img/dc92cdd2d9368bc6fec7de1b0c6ebd80.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rd_H9BrD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ffs8af2416sy67nneuqx.png) 
*参见[https://V8 . dev/features/support](https://v8.dev/features/support)*上的支持

`Promise.race`如果要履行各种承诺也很有用:

1.  用第一个成功的结果来做某事(如果其中一个承诺得到履行)；或
2.  一旦其中一个承诺被拒绝就做点什么。

也就是说，如果其中一个承诺被拒绝，您要保留此拒绝，以便单独处理错误情况。下面的例子正是这样做的:

```
try {
  const result = await Promise.race([
    performHeavyComputation(),
    rejectAfterTimeout(2000),
  ]);
  renderResult(result);
} catch (error) {
  renderError(error);
} 
```

我们开始了一项计算密集型任务，可能需要很长时间，但我们违背了 2 秒钟后被拒绝的承诺。根据要履行或拒绝的第一个承诺，我们将在两个不同的代码路径中呈现计算结果或错误消息。

# 承诺

[![](img/2eb8b63164bd041dfddb42d7c004a2b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zi3dEb4O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/2060/1%2AZKLq7j_MpTrID5ht025pTA.png) 
*参见[https://V8 . dev/features/support](https://v8.dev/features/support)*上的支持

`Promise.allSettled`当所有入境承诺都被决定时给你一个信号，这意味着它们被决定或拒绝。这在不关心承诺状态的情况下很有用，只想知道工作何时完成，而不管是否成功。

例如，您可以启动一系列独立的 API 调用，并使用“
”确保在执行其他操作(例如删除加载旋转控件)之前完成所有调用:

```
const promises = [
  fetch('/api-call-1'),
  fetch('/api-call-2'),
  fetch('/api-call-3'),
];
// Imagine que algumas dessas requisições falhem e outras são bem-sucedidas

await Promise.allSettled(promises);

// Todas as chamadas de API foram finalizadas (falhas e sucessos)
removeLoadingIndicator(); 
```

# 许诺

[![](img/618c42d6453f858119447dc018481d68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UPiculaj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/2024/1%2A1mF4tA93h53iLYeScGHM8g.png) 
*参见[https://V8 . dev/features/support](https://v8.dev/features/support)*上的支持

`Promise.any`一旦其中一个诺言兑现，就会给你一个信号。这与`Promise.race`相似，只是`any`当其中一个承诺被拒绝时不要提前拒绝。

```
const promises = [
  fetch('/endpoint-a').then(() => 'a'),
  fetch('/endpoint-b').then(() => 'b'),
  fetch('/endpoint-c').then(() => 'c'),
];
try {
  const first = await Promise.any(promises);
  // Qualquer uma das promessas foi cumprida.
  // → e.g. 'b'
  console.log(first);
} catch (error) {
  // Todas as promessas foram rejeitadas.
  console.log(error);
} 
```

此代码示例检查哪个端点响应最快并记录。只有当所有的订单都失败了，我们才会在`catch`区块结束，在那里我们才能处理错误。

# [t1【学分】](#cr%C3%A9ditos-%EF%B8%8F)

*   [承诺组合](https://v8.dev/features/promise-combinators)，escrito originate por[马蒂亚斯·拜恩斯](https://twitter.com/mathias)**
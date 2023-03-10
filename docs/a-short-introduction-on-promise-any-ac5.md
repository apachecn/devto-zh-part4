# Promise.any 的简短介绍

> 原文：<https://dev.to/tjmonsi/a-short-introduction-on-promise-any-ac5>

*注*:你可以在 https://github.com/tc39/proposal-promise-any[看到该提案的现阶段情况](https://github.com/tc39/proposal-promise-any)

## 描述

需要履行的不确定数量的承诺。它返回一个承诺，由第一个成功解析的承诺来履行，并传回结果值。

如果它们都失败了，`Promise.any`抛出一个异常并执行承诺的拒绝部分，同时列出所有的异常。

## 韩能做到这样对吗？

操作类似，但有一个主要区别。如果任何传入的承诺返回错误，`Project.race`将拒绝，而不管其他承诺的状态。如果任何包含的承诺返回一个错误，一个解析的承诺，即使它首先执行，仍然会导致拒绝。

## 我们之前是怎么做到的？

在引入`Promise.any`之前，完成这个功能的模式看起来像这样。

```
const p1 = new Promise(/* ... */);
const p2 = new Promise(/* ... */);
const promises = 2;
const errorList = [];
let result = null;

const thenFn = (r) => {
  if (result) return;
  result = r;
  /* do something with result */
}

const errorHandler = (err) => {
  if (errorList.length !== promises) {
    return errorList.push(err);
  }
  /* handle all errors in errorList */
}

p1.then(thenFn).catch(errorHandler);
p2.then(thenFn).catch(errorHandler); 
```

Enter fullscreen mode Exit fullscreen mode

## 现在怎么做？

用`Promise.any`完成同样事情的语法如下所示。这类似于使用`Promise.race`或`Promise.all`。

```
const p1 = new Promise(/* ... */);
const p2 = new Promise(/* ... */);
const promises = [ p1, p2 ];

Promise.any(promises)
  .then(firstResult => { /* do whatever */ })
  .catch(allErrors => { /* do whatever */ })

// or when using async/await

try {
  const firstResult = await Promise.any(promises);
  /* do whatever */
} catch (allErrors) {
  /* do whatever */
} 
```

Enter fullscreen mode Exit fullscreen mode

## 在什么情况下我们会想用这个？

假设您想要获取几个数据源，包括来自缓存 API 的数据。目标是尽可能快地检索数据，但是不需要多次检索。

```
try {
  const url = 'https://example.com/api/v1/some-endpoint/data';
  const data = await Promise.any([
    fetch(url),
    fetch('https://example.com/api/v1/some-endpoint-that-hopefully-is-near/data'),
    cache.match(url)
  ]);

  /* render back to browser */
} catch (errors) {
  /* render all error or a general error  */
} 
```

Enter fullscreen mode Exit fullscreen mode

## 大喊

感谢 [laurieontech](https://twitter.com/laurieontech) 作为本文的第二双眼睛，以及 [JS 外展小组](https://github.com/js-outreach/js-outreach-groups)给我这个机会。

## 参考文献

*   [https://github.com/tc39/proposal-promise-any](https://github.com/tc39/proposal-promise-any)
*   [https://esdiscuss.org/topic/promise-any](https://esdiscuss.org/topic/promise-any)
# 用 async/await 处理数组中的承诺

> 原文：<https://dev.to/afifsohaili/dealing-with-promises-in-an-array-with-async-await-5d7g>

Promises and `async/await`是新版本 JavaScript 的一个受欢迎的补充。如果你还没有使用它，并且被困在*回调地狱*，你可能想要检查它并且已经开始使用它。相信我，很牛逼！[MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)将是一个很好的起点，并且 [CSS-Tricks](https://css-tricks.com/using-es2017-async-functions/) 也有一篇关于它的好文章。

但是当使用`async/await`来处理一组承诺时，就有点棘手了。谢天谢地，这是我根据自己的经验写的对付它们的备忘单。

*附:没有外部库！*😉

现在，让我们开始吧！假设我们有以下异步函数:

```
const resolveInTwoSeconds = () => {
  return new Promise((resolve) => {
    setTimeout(() => resolve(2), 2000);
  })
};

const resolveInThreeSeconds = () => {
  return new Promise((resolve) => {
    setTimeout(() => resolve(3), 3000);
  })
};

const resolveInFiveSeconds = () => {
  return new Promise((resolve) => {
    setTimeout(() => resolve(5), 5000);
  })
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 1。等待所有承诺以 Promise.all 完成

接受一个承诺数组，并返回一个新的承诺，该承诺只有在数组中的所有承诺都被解析后才被解析。承诺解析为每个承诺返回的所有值的数组。

```
(async function() {
  const asyncFunctions = [
    resolveInTwoSeconds(),
    resolveInThreeSeconds(),
    resolveInFiveSeconds()
  ];
  const results = await Promise.all(asyncFunctions);
  // outputs `[2, 3, 5]` after five seconds
  console.log(results);
})(); 
```

Enter fullscreen mode Exit fullscreen mode

## 2。等待至少一个承诺，完成承诺。赛跑

接受一个承诺数组，并返回一个新的承诺，当数组中的一个承诺被解析时，该承诺将立即解析，并返回该承诺的值。

```
(async function() {
  const asyncFunctions = [
    resolveInTwoSeconds(),
    resolveInThreeSeconds(),
    resolveInFiveSeconds()
  ];
  const result = await Promise.race(asyncFunctions);
  // outputs `2` after two seconds
  console.log(result);
})(); 
```

Enter fullscreen mode Exit fullscreen mode

## 3。等待所有承诺逐一完成

实现这一点最简单、最直接的方法是使用普通的老式`for`循环。它可读性强，也容易推理。

```
(async function() {
  const asyncFunctions = [resolveInTwoSeconds, resolveInThreeSeconds, resolveInFiveSeconds];
  // outputs 2 after 2 seconds
  // outputs 3 after 5 seconds
  // outputs 5 after 8 seconds
  for (const asyncFn of asyncFunctions) {
    const result = await asyncFn();
    console.log(result);
  }
})(); 
```

Enter fullscreen mode Exit fullscreen mode

*更新:下面的方法是帖子中的原始方法，但经过多年的新经验，我已经意识到这不必要地使事情变得复杂，我只是将`reduce`编程为类似于`for`循环的行为。建议:为此只使用 for 循环。有兴趣的话还留在这里*

在`Promise`类中没有本地方法可以快速做到这一点，但是我们可以利用`Array.prototype.reduce`方法来实现这个目标。

```
(async function() {
  const asyncFunctions = [resolveInTwoSeconds, resolveInThreeSeconds, resolveInFiveSeconds];
  // outputs 2 after 2 seconds
  // outputs 3 after 5 seconds
  // outputs 5 after 8 seconds
  await asyncFunctions.reduce(async (previousPromise, nextAsyncFunction) => {
    await previousPromise;
    const result = await nextAsyncFunction();
    console.log(result);
  }, Promise.resolve());
})(); 
```

Enter fullscreen mode Exit fullscreen mode

这比以前的实现不太直接，但是我将写一篇单独的文章来解释这一点。让我们保留这个帖子只是为了快速检查😉。

## 4。逐批运行异步函数，每批函数并行执行

如果您想避免达到某些 API 服务的速率限制，这真的很有帮助。这利用了#3 中的相同概念，其中我们有一个顺序解析的承诺数组，结合了一个二维承诺数组和`Promise.all`的使用。

这里的关键是首先在二维数组中构建异步函数的集合。一旦有了这些，我们就可以迭代每个异步函数集合并并行执行它们，并使用`Promise.all`等待每个函数完成。在当前批中的所有承诺解决之前，我们不会处理下一批。

```
(async function() {
  const asyncFunctionsInBatches = [
    [resolveInTwoSeconds, resolveInTwoSeconds],
    [resolveInThreeSeconds, resolveInThreeSeconds],
    [resolveInFiveSeconds, resolveInFiveSeconds],
  ];

  // Outputs [2, 2] after two seconds
  // Outputs [3, 3] after five seconds
  // Outputs [5, 5] after eight seconds
  for (const currentBatch of asyncFunctionsInBatches) {
    const currentBatchPromises = currentBatch.map(asyncFn => asyncFn())
    const batchResults = await Promise.all(currentBatchPromises)
    console.log(batchResults)
  }
})(); 
```

Enter fullscreen mode Exit fullscreen mode

*更新:再一次，下面的方法是帖子中的原始方法，但是经过多年的新经验，我已经意识到这不必要地使事情变得复杂，我只是将`reduce`变成了一个`for`循环。建议:为此只使用 for 循环。有兴趣的话还留在这里*

下面是上述概念的完整实现:

```
(async function() {
  const asyncFunctionsInBatches = [
    [resolveInTwoSeconds, resolveInTwoSeconds],
    [resolveInThreeSeconds, resolveInThreeSeconds],
    [resolveInFiveSeconds, resolveInFiveSeconds],
  ];

  // Outputs [2, 2] after two seconds
  // Outputs [3, 3] after five seconds
  // Outputs [5, 5] after eight seconds
  await asyncFunctionsInBatches.reduce(async (previousBatch, currentBatch, index) => {
    await previousBatch;
    console.log(`Processing batch ${index}...`);
    const currentBatchPromises = currentBatch.map(asyncFunction => asyncFunction())
    const result = await Promise.all(currentBatchPromises);
    console.log(result);
  }, Promise.resolve());
})(); 
```

Enter fullscreen mode Exit fullscreen mode

请记住，我在这里是通过硬编码来构建异步函数批处理的。在实际的应用程序中，您可能会从 API 调用等返回一个动态长度的数组，所以您必须自己将它们分开。该任务的快速实现:

```
const splitInBatch = (arr, batchSize) => {
  return arr.reduce((accumulator, element, index) => {
    const batchIndex = Math.floor(index / batchSize);
    if (Array.isArray(accumulator[batchIndex])) {
      accumulator[batchIndex].push(element);
    } else {
      accumulator.push([element]);
    }
    return accumulator;
  }, []);
}

// outputs [[1, 2, 3], [4, 5, 6]]
console.log(splitInBatch([1, 2, 3, 4, 5, 6], 3)); 
```

Enter fullscreen mode Exit fullscreen mode

或者，您也可以选择像`lodash`这样的库来帮助您完成这项任务。

```
import chunk from 'lodash.chunk';

// outputs [[1, 2, 3], [4, 5, 6]]
console.log(chunk([1, 2, 3, 4, 5, 6], 3)); 
```

Enter fullscreen mode Exit fullscreen mode

## 5。额外提示:不要将异步函数传递给 forEach

记住，`Array.prototype.map`和`Array.prototype.forEach`的区别在于，后者并不返回每次迭代的结果。如果我们将`async`函数传递给`forEach`，我们就没有办法取回返回的承诺来做任何有用的事情。除非你想启动异步函数并忘掉它，否则将异步函数传递给`forEach`永远不是你想做的事情。

### 结论

这就对了。这是关于一系列承诺中该做什么和不该做什么的 5 张备忘单。我希望这对你们所有人都有用😁，如果我有什么需要改进的地方，请在评论区告诉我。

再见！
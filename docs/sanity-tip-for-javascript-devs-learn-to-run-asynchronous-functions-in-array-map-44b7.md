# JavaScript 开发人员的明智技巧:学习在 Array.map()中运行异步函数

> 原文：<https://dev.to/mjraadi/sanity-tip-for-javascript-devs-learn-to-run-asynchronous-functions-in-array-map-44b7>

### 问题

我最近使用 Node.js 编写了一些 API 代码，遇到了一个地方，我需要在每个项目上使用带有`async`函数的`Array.map()`。当我的地图调用不能正常工作时，我感到很惊讶。记录地图函数的输出会显示如下:

```
[
  Promise { <pending> },
  Promise { <pending> },
  Promise { <pending> },
  Promise { <pending> },
  Promise { <pending> },
] 
```

Enter fullscreen mode Exit fullscreen mode

我期望得到一个对象数组，每个对象都将从 map 函数中的异步 API 调用返回，但是得到了这个。

### 为什么在`Array.map()`里面使用异步函数

`Array.map()`是一个同步操作，对数组中的每个元素运行一个函数，产生一个包含更新项的新数组。有些情况下，您希望在 map 中运行异步功能，例如，更新模型列表，并将更改后的信息推回数据库，或者从 API 请求信息，以便用于进一步的操作。
让我们来解决在同步函数中运行异步操作的问题！

### 解:`Promise.all`

在`array.map()`中执行异步动作的一种方法是为每个项目返回一个承诺，然后在 map 函数之外进行解析。因为 map 不会等待承诺解决，它将返回一个待定的承诺。

在使用结果之前，您需要处理从 map resolve 返回的数组中的所有承诺。用`Promise.all(<array-of-promises>)`做这个。等待所有承诺完成的结果是另一个包含结果的数组。

让我们用一个例子来形象化这个想法:

```
const list = [] //...an array filled with values

const functionWithPromise = item => { //a function that returns a promise
  return Promise.resolve('ok')
}

const anAsyncFunction = async item => {
  return await functionWithPromise(item)
}

const getData = async () => {
  return await Promise.all(list.map(item => anAsyncFunction(item)))
}

const data = getData()
console.log(data) 
```

Enter fullscreen mode Exit fullscreen mode

`async` / `await`是我最喜欢的 JavaScript 新特性之一，因为它使得异步 JavaScript 代码更具可读性。如果你和我一样，你会发现`Promise.all`在和`async` / `await`一起工作时非常有用。

要注意的主要事情是`Promise.all()`的使用，当它的所有承诺都解决时，它就解决了。`list.map()`返回一个承诺列表，所以当我们运行的所有东西都被解析时，我们将得到这个值。记住，我们必须将任何调用`await`的代码包装在`async`函数中。
我希望这对你有所帮助。请务必在下面留下您的评论或任何问题。

### 关于我

我是一名全栈 web 开发人员，也是一个由才华横溢的专业自由开发人员组成的小组 Bits n Bytes Dev Team 的联合创始人，我们提供基于尖端技术的定制 web 应用程序开发服务，以满足客户独特的业务需求。

我可以被雇佣，你可以通过[https://www.bitsnbytes.ir/portfolio](https://www.bitsnbytes.ir/portfolio)查看我的投资组合网站，或者通过 [raadi@bitsnbytes.ir](mailto:raadi@bitsnbytes.ir) 联系我。
# JavaScript 中的异步性

> 原文：<https://dev.to/munkacsimark/asynchronicity-in-javascript-50f9>

当事情可以独立于程序的主线程发生时，我们谈论的是[异步](https://en.wikipedia.org/wiki/Asynchrony_(computer_programming))。默认情况下，JavaScript 是一种同步[单线程](https://en.wikipedia.org/wiki/Thread_(computing)#Single_vs_multiprocessor_systems)语言，这意味着它不能并行运行多个线程。您的代码将从上到下一个接一个地执行。但是，如果您需要在客户端进行一些繁重的计算，或者只是为了等待长时间的服务器响应，该怎么办呢？用户界面冻结，直到它完成，没有点击，没有滚动，没有动画。

JavaScript(顾名思义)是一种由它的[引擎](https://en.wikipedia.org/wiki/JavaScript_engine)在浏览器中运行的[高级](https://en.wikipedia.org/wiki/High-level_programming_language)、[解释的](https://en.wikipedia.org/wiki/Interpreted_language)、[脚本语言](https://en.wikipedia.org/wiki/Scripting_language)。更多关于发动机的信息，这里是 [Gecko](https://developer.mozilla.org/en-US/docs/Mozilla/Gecko) s 和 [V8](https://v8.dev/) s 的主页。浏览器提供了可以处理异步功能的特性。Web API、事件循环和任务队列都不是 JavaScript 引擎的一部分。要更深入地了解事件循环是如何工作的，请观看这个精彩的视频。

[https://www.youtube.com/embed/cCOL7MC4Pl0](https://www.youtube.com/embed/cCOL7MC4Pl0)

## 回调

回调是作为参数传递给另一个函数的函数。回调最简单的例子是处理按钮点击。您需要监听 click 事件，当它发生时，浏览器将执行给定的函数(回调)。

```
const button = document.getElementById('button');
const myCallback = () => alert('Click happened');

button.addEventListener('click', myCallback); 
```

Enter fullscreen mode Exit fullscreen mode

这样，您也可以处理异步服务器请求。

```
const request = new XMLHttpRequest();
const myCallback = event => console.log(event.target.response);

request.addEventListener('load', myCallback);
request.open('GET', 'http://www.example.org/example.txt');
request.send(); 
```

Enter fullscreen mode Exit fullscreen mode

回调适用于简单的情况，比如处理按钮点击。当您需要嵌套回调并将逻辑封装到。它被称为“回调地狱”或“末日金字塔”。例如，让我们等待页面加载，然后监听按钮点击，当按钮被点击时，执行服务器请求，然后将其记录到控制台。

```
window.addEventListener('load', () => {
  document.getElementById('button').addEventListener('click', () => {
    const request = new XMLHttpRequest();
    request.addEventListener('load', (event) => {
      console.log(event.target.response);
    });
    request.open('GET', 'http://www.example.org/example.txt');
    request.send();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 承诺

在 ES6 中有一个新功能叫做[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。它是一个表示异步操作最终完成或失败的对象。它的构造函数等待一个带有参数“resolve”和“reject”的 exectutor 函数。您可以使用“then”方法作为实现和拒绝处理程序，“catch”用于仅处理拒绝，而“finally”用于在承诺完成时运行代码。例如，让我们把超时包装成承诺。

```
const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('Hello from promise');
  }, 2000);
});

myPromise.then(returnedValue => console.log(returnedValue)); 
```

Enter fullscreen mode Exit fullscreen mode

当您需要嵌套异步部分，但又想避免回调地狱时，承诺是很方便的。在这个例子中，我将使用 [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) ，它返回一个承诺。让我们创建一个带有超时的代码片段。之后，进行服务器请求，然后注销数据。

```
const timeout = () => new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('Hello from promise');
  }, 2000);
});

const request = () => fetch('http://www.example.org/example.txt')

timeout()
  .then(request)
  .then(response => response.json())
  .then(data => console.log(data)); 
```

Enter fullscreen mode Exit fullscreen mode

## 异步/等待

ES7 带来了 [async](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) 和[wait](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await)语法，它们只是承诺之上的语法糖衣。“await”只能在异步函数中使用。有了它们，你可以用清晰易读的语法等待承诺。让我们用 async/await 从上面重构代码。

```
const timeout = () => new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('Hello from promise');
  }, 2000);
});
const request = async () =>
  await fetch('http://www.example.org/example.txt');

await timeout();
const request = await request();
console.log(request.json()); 
```

Enter fullscreen mode Exit fullscreen mode
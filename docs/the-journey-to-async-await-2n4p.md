# 异步/等待之旅

> 原文：<https://dev.to/shortdiv/the-journey-to-async-await-2n4p>

Async/Await 已经出现，或者至少已经出现了一段时间。不管怎样，web 的很大一部分还没有迁移，今天许多代码库还在继续使用回调。冒着被规定的风险，与回调相比，async/await 是一种非常直观的处理异步 JavaScript 的方式。让我们深入了解是什么让 async/await 变得如此强大，以及我们如何让从回调的迁移变得轻而易举。

## 异步 JS 的到来

一开始，JavaScript 是纯同步的，这意味着代码只能按顺序执行。有一段时间，这意味着代码必须从上到下顺序编写。随着时间的推移，JavaScript 被用于更复杂的操作，这种范式变得有局限性，因为发生的任何动作都会阻塞主线程，导致“janky”用户体验。2005 年 AJAX/XHR 操作的引入为 JS 带来了异步功能。这允许在不阻塞主线程的情况下向服务器发出请求。

随着 async JS 的出现，全能回调也被引入。回调是一个简单的函数，它作为参数传递给另一个函数，只有当包含它的“父”函数返回时才会执行。通过回调，您可以始终确保代码的操作顺序。

事件侦听器是 JavaScript 中使用回调的异步操作的一个常见示例。

```
document.querySelector("button").addEventLister("click", () => {
  // run a function //
}); 
```

另一个常见的例子是`setTimeout`函数，它允许您在特定时间过后执行一个函数。

```
setTimeout(() => {
  // run some function after 5s
}, 5000); 
```

## 他们称之为回调地狱

回调是声明一个特定操作完成后必须发生的动作的极好方式。然而，使回调如此臭名昭著的是链接一系列请求和/或试图从异步函数返回数据所带来的复杂性。

假设我们正在尝试访问 Google API。为此，我们必须使用 oauth 认证请求，并使用生成的访问令牌向相关的 API 服务发出请求。因为事件必须以特定的顺序发生，所以回调是必要的。在一个`fetchData`函数中，我们将首先创建一个 google auth 实例[a]。然后，我们将把从 Google 的 oauth 过程生成的特定代码传递给 auth 实例[b]上可访问的方法`getToken`。一旦从`getToken`方法创建了访问令牌，我们将获取令牌，在 oauth 实例[c]上设置它，最后运行回调[d]。

```
const { google } = require("googleapis");

function fetchData(code, callback) { // a
  const { CLIENT_SECRET, CLIENT_ID, REDIRECT_URIS } = process.env;
  const oAuth2Client = new google.auth.OAuth2(
    `${CLIENT_ID}`,
    `${CLIENT_SECRET}`,
    `${REDIRECT_URIS}`
  );
  oAuth2Client.getToken(code, (err, token) => { // b
    if (err) return console.error("Error retrieving access token", err);
    oAuth2Client.setCredentials(token); // c
    callback(oAuth2Client); // d
  });
} 
```

虽然乍一看这似乎很简单，但是确保`fetchData`函数从 Google API 返回数据需要欺骗数据流。我们上面的当前设置虽然有效，但返回 undefined。发生这种情况是因为根据定义，回调从不返回值，它们只是用给定的结果执行回调。因此，用现有的回调流来修复我们的函数是不可能的。 *womp womp*

确保异步函数返回值的一种方法是，确保包装它的所有函数也是异步的。这就是承诺派上用场的地方。

## 承诺

承诺被定义为“一种价值的代理，这种价值最终将变得可用”。由于它们的异步特性，它们非常适合处理从异步函数返回的值。承诺通常有三种状态:待定、已解决或已拒绝。当第一次被调用时，promise 将以挂起状态开始，当从它包装的函数接收到返回值时，它或者解析或者拒绝并分别调用回调函数 then 和 catch。这里有一个例子，用上面提到的 setTimeout 的例子来说明承诺是什么样子的。

```
new Promise((resolve, reject) => {
  setTimeout(() => {
  // run some function after 5s
  resolve('some value')
  }, 5000);
}) 
```

我们所做的唯一更改是将 setTimeout 包装在一个承诺中，并添加一行来解析带有`some value`的函数。如果我们要调用上面的函数，我们现在将能够获取返回值并将其记录到控制台(如果我们愿意的话)。promises 的一个附加特性是错误处理更加直观。不需要在不清楚错误来源的情况下将错误记录到控制台，promises 允许错误冒泡并适当地处理它们。

```
function timeout() {
  return new Promise((resolve, reject) => {
    setTimeout(() => { ... })
  })
}

timeout()
  .then(res => {
    console.log(res)
  })
  .catch(err => {
    console.error(err)
  }) 
```

让承诺适应我们给定的 Google API 很容易。我们所要做的就是将对`getToken`的异步函数调用包装在一个承诺中。这样，我们可以确保我们对`fetchData`的整体函数确实返回了一个我们可以使用的值。

```
function fetchData (code, callback) {
  const { CLIENT_SECRET, CLIENT_ID, REDIRECT_URIS } = process.env;
  oAuth2Client = new google.auth.OAuth2(
      `${CLIENT_ID}`,
      `${CLIENT_SECRET}`,
      `${REDIREC_URIS}`
  );
  return new Promise((resolve, reject) => {
      return oAuth2Client.getToken(code, (err, token) => {
        if (err) reject(err);
        oAuth2Client.setCredentials(token);
        console.log(callback(oAuth2Client));
        resolve(callback(oAuth2Client));
      })
  })
} 
```

让我们给我们的例子增加额外的复杂性来测试承诺。假设我们在`fetchData`函数中引用的回调也是一个使用承诺的异步操作。我们对`callback`的调用现在会附带回调来捕获成功或错误状态。随着连续异步函数调用数量的增加，这个曾经不起眼的函数的复杂性也在增加。

```
 function fetchData (code, callback) {
  const { CLIENT_SECRET, CLIENT_ID, REDIRECT_URIS } = process.env;
  oAuth2Client = new google.auth.OAuth2(
      `${CLIENT_ID}`,
      `${CLIENT_SECRET}`,
      `${REDIREC_URIS}`
  );
  return new Promise((resolve, reject) => {
      return oAuth2Client.getToken(code, (err, token) => {
        if (err) reject(err);
        oAuth2Client.setCredentials(token);
        console.log(callback(oAuth2Client));
        callback(oAuth2Client)
          .then(res => { resolve(res) })
          .catch(err => { reject(err) })
      })
  })
} 
```

## 异步/等待

正如 promises 上的节点文档所解释的，async/await 是 promises 和 generators 的组合，基本上是 promises 的更高层次的抽象。Async 用于将函数定义为异步，await 声明包装外部异步函数必须等待的内部函数。

因为 async/await 仍然使用 promises，所以让我们重用我们之前的 promised`setTimeout`例子。然而，我们不使用`then`和`catch`回调来获取返回值，而是使用 async/await。这里值得一提的是，用`async`前置任何函数总会返回一个承诺。所以要把这个函数变成一个使用 async/await 的函数，我们可以简单地在函数前面加上`async`关键字。

我们将更进一步，将我们的函数调用包装在一个`try…catch`块中，这样当错误发生时，我们可以适当地处理它们。 *Tada！*T3】

```
 async function timeout() {
  return new Promise((resolve, reject) => {
    setTimeout(() => { ... })
  })
}

try {
  const time = timeout()
  console.log(time)
} catch(err) {
  console.error(err)
} 
```

现在我们已经对 async/await 有所了解，让我们重构 Google API 代码来使用它。我们首先将调用函数定义为 async，然后在其中，我们将预先考虑对 getToken 和 setCredentials 的 oauth 调用，并使用 await。这允许我们承诺我们的异步 oauth 调用，并确保主函数只在所有承诺都被解决或拒绝时才返回。我们还将在 try…catch 块中包装我们的 oauth 调用，以确保任何错误一出现就被捕获。

```
async function fetchData(code, callback) {
    const { CLIENT_SECRET, CLIENT_ID, REDIRECT_URIS } = process.env;
    oAuth2Client = new google.auth.OAuth2(
      `${CLIENT_ID}`,
      `${CLIENT_SECRET}`,
      `${REDIREC_URIS}`
    );
    try {
      let token = await oAuth2Client.getToken(code);
      await oAuth2Client.setCredentials(token.tokens);
      return callback(oAuth2Client)
    } catch (e) {
      return console.error("Error retrieving access token", e);
    }
} 
```

## Async/Await 将继续存在

从上面的例子可以看出，从回调转移到 async/await 并不困难。虽然使用 async/await 需要从回调进行某种范式转换，但 async/await 在更直观的错误处理和更容易的调试方面带来的好处使切换成为一个令人信服的理由。
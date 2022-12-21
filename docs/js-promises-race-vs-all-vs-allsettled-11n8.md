# JS 承诺:种族 vs 所有 vs 所有定居

> 原文：<https://dev.to/bnevilleoneill/js-promises-race-vs-all-vs-allsettled-11n8>

## 什么，为什么，什么时候

[![](img/acc22f5862ec630b3e3e77536178ca1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xDkv95Ao--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fgl2z7nc6msttoh3hiu5.png)

承诺已经存在一段时间了，但是直到 ES6，我们被迫通过第三方库来使用它们，并且实现和 API 彼此略有不同。令人欣慰的是，ES6 通过实现原生的 [Promise 对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)来标准化 API，允许每个人放弃第三方实现。

也就是说，也许你像我一样，因为它需要一个第三方库，你忽略了承诺，依赖回调和库，如 [async.js](https://caolan.github.io/async/v3/) 来处理代码，避免陷入回调地狱(或也称为末日金字塔)。

但是既然承诺是天生的，就没有借口再忽视它们了。因此，在本文中，我想介绍三种方法，它们将帮助您处理一些更复杂的用例，同时还能同时处理多个承诺。

但是首先，我想介绍一下基于承诺的语法带来的一个主要好处。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## 声明式编程

通过使用方法链接语法的过程，以及方法名背后的逻辑(即 then 和 catch)，我们可以构建一个代码块，该代码块关注于*声明*的意图。而不是具体说明它需要如何做我们需要的事情。

让我解释一下。如果你想获取列表中的每个数字，并使其翻倍，会怎么样？你会怎么做？

我们通常学习编写代码的方式是像计算机一样思考:

你需要迭代列表中的每一项，所以你需要一个位置计数器，它需要从 0 到数组中的数字数量，对于每个数字，你需要加倍它，并可能把它添加到另一个不同的数组中。

翻译过来就是:

```
let list = [1,2,3,4,5];
let results = []
for(let counter = 0; counter &lt; list.length; counter++) {
       results[i] = list[i] * 2;
}
console.log(results);
//[2,4,6,8,10] 
```

Enter fullscreen mode Exit fullscreen mode

现在，我的建议是，想想需要发生什么，然后写下来。换句话说:

将每个数字映射成它的双精度数。

```
let list = [1,2,3,4,5];
let results = list.map( i => i * 2 );

console.log(results);
//[2,4,6,8,10] 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常简单的例子，但是它展示了*声明式编程背后的力量。*

方法上的一个简单改变可以帮助你写出更清晰、更易读的代码。阅读第二个例子背后的认知负荷比第一个例子低得多，因为当使用`for`循环时，您必须在心里解析代码并逐行执行，而`map`是您可以在更高层次上快速解释的东西。

以这种方式编写代码的另一个好处是，您可以开始考虑数据需要经历的转换或步骤。

我给你看:

```
authenticateUser(usrname, pwd, (err, isAuth) => {
    if(err) return dealWithYourErrors(err);
    if(!isAuth) return dealWithUnauthorizedAccess(usrname);
    getSessionToken(usrname, (err, token) => {
        if(err) return dealWithYourErrors(err);
        loadUserDetails(usrname, (err, details) => {
            if(err) retun dealWithYourErrors(err);
            let user = new User(usrname, token, details);
            performAction(user, (err, result) => { //this is what you wanted to do all along
                if(err) return dealWithYourErrors(err);
                sendBackResponse(result);
            })
        })
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

上面是嵌套回调的一个经典例子，其中有几条信息需要从不同的服务中获取(或者由于其他一些逻辑，在不同的步骤中获取)。

默认情况下，回调只允许您串行处理异步行为，在这种情况下，这并不理想。`getSessionToken`和`loadUserDetails`都可以并行完成，因为它们不需要彼此的结果来执行它们的操作。

遗憾的是，这样做需要一些额外的代码，比如使用 async.js 或编写自己的逻辑。

此外，代码的整个结构是必不可少的，因为它明确说明了如何处理错误以及如何处理串行调用。您(从事此项工作的开发人员)需要在编写这些步骤时考虑它们，以确保正确的行为。

让我向你展示一个基于承诺的方法是如何写的:

```
authenticateUser(username, pwd)
    .then( preActions )
    .then( performAction )
    .catch(dealWithYourErrors); 
```

Enter fullscreen mode Exit fullscreen mode

我相信我们都同意，这是一个简单得多的写作和阅读。让我向您展示这些函数的模拟实现，因为所有这些函数中都需要返回承诺:

```
function authenticateUser(usr, pwd){ //main function called by the developer
    return new Promise( (resolve, reject) => {
        //auth logic goes here...
        resolve(usr); //assuming usr and pwd are valid...
    })
}
/** once logged in, we'll need to get the session token and load the user's details
*/
function preActions(usrname) { 
    return Promise.all([getSessionToken(usrname), loadUserDetails(usrname)]);
}

function getSessionToken(usrname) {
    return new Promise( (resolve, reject) => {
        //logic for getting the session token
        resolve("11111")
    })
}
function loadUserDetails(usrname) {
    return new Promise( (resolve, reject) => {
        //here is where you'd add the logic for getting the user's details
        resolve({name: 'Fernando'});
    })
}
function performAction() {
    //the actual action: we're just logging into stdout the arguments recevied
    console.log(arguments);
}
function dealWithYourErrors(err) {
    console.error(err);
} 
```

Enter fullscreen mode Exit fullscreen mode

以下是上述代码的亮点:

*   `preActions`使用本地`Promise`对象的`all`方法并行调用两个函数。如果他们中的任何一个失败了(从而拒绝了他们各自的承诺)，那么整个集合都将失败，并且`catch`方法将被调用
*   其他人只是在归还承诺

上面的例子完美地过渡到了我想介绍的第一个方法:`all`。

## 无极法

当您不得不处理多个并行异步调用时，`all`方法非常适合，它允许您鱼与熊掌兼得。

根据定义，`Promise.all`将运行您的所有承诺，直到满足以下条件之一:

*   它们都解析，这将依次解析该方法返回的承诺
*   其中一个失败，这将立即拒绝承诺返回

关于`Promise.all`要记住的是最后一点:你不能处理部分失败。如果其中一个承诺被拒绝，那么整个过程就会停止，并调用失败回调。如果被拒绝的承诺不是在做任务关键的事情，并且其内容可能会丢失，这就不理想了。

考虑一个搜索服务，它从主数据库获取数据，并使用外部服务来丰富结果。这些外部服务不是必需的，它们只是为了帮助您提供更多信息(如果有的话)。

在搜索过程中，让这些第三方服务失败会导致此方法失败，从而暂停搜索过程并阻止向您的用户返回有效的搜索结果。

正是在这里，你希望你的内在逻辑允许你所有的承诺被执行，忽略可能的拒绝。

## 输入 Promise.allSettled

如果您来自上述用例，这是您所有问题的解决方案。遗憾的是，这个方法还不是 JavaScript 的一部分。让我解释一下:这是一项正在考虑和审查的拟议增加的内容。但遗憾的是，它还不是这门语言的一部分。

也就是说，考虑到外部[实现的数量](https://www.npmjs.com/search?q=allsettled)，我还是考虑了一下。

它的要点是，与前面的方法不同，这个方法不会在第一个承诺被拒绝时失败，相反，它将返回一个值列表。这些值将是具有两个属性的对象:

1.  退回承诺的状态(“已拒绝”或“已履行”)
2.  已履行承诺的价值或拒绝承诺的原因

```
var allSettled = require('promise.allsettled');

var resolved = Promise.resolve(42);
var rejected = Promise.reject(-1);

allSettled([resolved, rejected]).then(function (results) {
    assert.deepEqual(results, [
        { status: 'fulfilled', value: 42 },
        { status: 'rejected', reason: -1 }
    ]);
});

allSettled.shim(); // will be a no-op if not needed

Promise.allSettled([resolved, rejected]).then(function (results) {
    assert.deepEqual(results, [
        { status: 'fulfilled', value: 42 },
        { status: 'rejected', reason: -1 }
    ]);
}); 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子展示了正在运行的实现，它是一个第三方库 [promise.allsettled](https://www.npmjs.com/package/promise.allsettled) 注意，但是它符合规范的最新版本。

*注意:*不要让方法的名字迷惑了你，很多人认为“allSettled”和“allResolved”意思一样，这是不正确的。承诺一旦得到*解决*或*拒绝*，则*解决*，否则，它就是*待定*。查看[状态和命运的完整列表](https://github.com/domenic/promises-unwrapping/blob/master/docs/states-and-fates.md)了解更多细节。

## 如果你想停止在第一个承诺上呢？

如果不是在第一个承诺失败后就停下来(很像`Promise.all`做的那样),而是在第一个承诺达成后就想停下来。

这是`Promise`对象允许您处理多个承诺的另一种方式，通过使用`race`方法，该方法不是试图解析所有承诺，而是实际上只是等待第一个完成，并根据承诺是被解析还是被拒绝而失败或成功。

是的，我在标题上有点作弊，因为如果第一件事是被拒绝的承诺，这个方法也会停止这个过程(就像`Promise.all`)。

但是不要注意这一点，让我们想想为什么你想要几个并行的承诺，并且只从第一个得到解决的承诺中得到结果。

## 什么时候用`race`？

信不信由你，有几个例子可以说明你为什么想用这种方法。现在让我给你两个例子:

### 第一步:性能检查

例如，如果性能是您的平台的重要组成部分，您可能希望拥有数据源的多个副本，并且您可以根据网络流量或其他外部因素，尝试查询所有副本，希望获得最快的副本。

您可以在没有承诺的情况下做到这一点，但是这种方法会有额外的开销，因为您必须处理逻辑以了解谁先返回以及如何处理其他未决的请求。

有了 promises 和`race`方法，您可以简单地专注于从所有来源获取数据，让 JavaScript 处理剩下的事情。

```
const request = require("request");

let sources = ["http://www.bing.com", "http://www.yahoo.com", "http://www.google.com" ];

let checks = sources.map( s => {
  return new Promise( (res, rej) => {
    let start = (new Date()).getTime()
    request.get(s, (err, resp) => {
        let end = (new Date()).getTime()
        if(err) return rej(err)
        res({
            datasource: s,
            time: end - start
        })
    })
  })
})

Promise.race(checks).then( r => {
  console.log("Fastest source: ", r.datasource, " resolved in: ", r.time, " ms")
}) 
```

Enter fullscreen mode Exit fullscreen mode

是的，代码有点基础，你可能有很多方法可以改进，但它表明了我的观点。我正在检查哪个数据源对我来说最快，而不必添加任何特殊的逻辑来处理异步解析。如果我想比较结果，我将不得不改为使用`Promise.allSettled`调用。

### 数字 2:加载指示器，我要显示吗？

另一个您可能想考虑使用这个方法的例子是当您试图决定是否在您的 UI 中显示一个加载指示器时。在创建 spa 时，一个很好的经验法则是您的异步调用应该为用户触发一个加载指示器，让他们知道正在发生一些事情。

但是当底层请求发生得非常快时，这个规则就不理想了，因为你可能在 UI 中得到的只是一条闪烁的消息，一些过得太快的消息。加载时间可能依赖于太多的东西，以至于您无法创建一个规则来知道何时显示指示器，以及何时不显示指示器而直接执行请求。

你可以玩拒绝和解决的概念，就像这样:

```
function yourAsynchronousRequest(params) {
  return new Promise((resolve, reject) => {
       //here is your request code, it'll resolve once it gets the actual data from the server
  });
}

function showDataToUser(params) {
  return yourAsynchronousRequest(params).then( data => console.log("data fetched:", data));
}

function timeout() {
  return new Promise((resolve, reject) => {
    setTimeout(() => reject(), TIMEOUTLIMIT); //TIMEOUTLIMIT is a constant you configured
  });
}

function showLoadingIndicator() {
  console.log("please wait...")
}

Promise.race([showDataToUser(), timeout()]).catch(showLoadingIndicator); 
```

Enter fullscreen mode Exit fullscreen mode

现在，竞争是针对实际的异步请求和设置为限制器的超时。现在，决定是否显示加载指示器的逻辑隐藏在`race`方法的后面。

## 最后的想法

承诺是有趣的，忽略它们不是我当年最好的举动之一，所以我非常高兴我已经决定将它们纳入我的日常编码习惯，如果你还没有，我强烈建议你也这样做。

如果你正在使用这些方法，请在评论中告诉我，我特别想知道你对`Promise.race`方法有什么样的用例！

下一次再见！

* * *

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[JS Promises:race vs all vs all settled](https://blog.logrocket.com/js-promises/)首先出现在 [LogRocket 博客](https://blog.logrocket.com)上。
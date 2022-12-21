# 美化你的延误

> 原文：<https://dev.to/aminnairi/beautify-your-delays-383o>

在这篇文章中，我将向你展示一个简单的技巧，让你的脚本在试图添加延迟时更具可读性。

## 2015 年 ECMAScript:承诺

在这篇文章中，我将讨论`async`和`await`关键字，但是在进一步讨论之前，我们首先必须知道 JavaScript 中的承诺是什么。

根据[文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise):

> Promise 对象表示异步操作的最终完成(或失败)及其结果值。

用人类的话来说，这意味着我们可以创建一个对象(记住，在 JavaScript 中一切都是对象？)可以解决(意味着像任何其他对象一样返回值)或抛出错误，但一切都不会阻塞我们脚本的主线程。一个简单的例子是在发出`console.log`之前通过`setTimeout`增加一些延迟。

## 以前是怎么做的

在 promise 之前，我们经常使用带有一些参数和一个回调函数的函数。在我们的例子中，回调将在`setTimeout`调用结束后被触发。下面是一个例子:

```
"use strict";

function sleep(seconds, callback) {
  setTimeout(callback, seconds * 1000);
}

const SECONDS = 5;

sleep(SECONDS, function() {
  console.log(`After ${SECONDS} seconds!`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

*[在线试用](https://repl.it/repls/NeglectedWrathfulLesson)*

看到了吗？没那么难。没有承诺就能做那种事情已经是可能的了，那么为什么还要费神去学习呢？现在让我们想象一下，我们的脚本变得更大了，我们现在必须等待三次，每次间隔三秒，并进行一些日志记录。我们经常会想到这样的事情:

```
"use strict";

function sleep(seconds, callback) {
  setTimeout(callback, seconds * 1000);
}

const SECONDS = 3;

sleep(SECONDS, function() {
  console.log(`First call after ${SECONDS} seconds!`);

  sleep(SECONDS, function() {
    console.log(`Second call after ${SECONDS} seconds!`);

    sleep(SECONDS, function() {
      console.log(`Third call after ${SECONDS} seconds!`);
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

*[在线试用](https://repl.it/repls/NutritiousHandsomeOs)*

现在想象你必须这样做十次。现在想象一下，不是在控制台上记录事情，而是必须对所有三个调用做一些特定的操作？它被称为[回调地狱](http://callbackhell.com/)，它在 JavaScript 中非常流行，尤其是在 Node.js 领域，直到 ECMAScript 标准发布了 Promise 对象。现在我们可以重新开始呼吸，写一些类似的东西:

```
"use strict";

// No more callback needed!
function sleep(seconds) {
  return new Promise(function(resolve) {
    setTimeout(function() {
      resolve();
    }, seconds * 1000);
  });
}

const SECONDS = 3;

sleep(SECONDS).then(function() {
  console.log(`First call after ${SECONDS} seconds!`);

  return sleep(SECONDS);
}).then(function() {
  console.log(`Second call after ${SECONDS} seconds!`);

  return sleep(SECONDS);
}).then(function() {
  console.log(`Third call after ${SECONDS} seconds!`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

*[在线试用](https://repl.it/repls/ThistleCalculatingDos)*

所以事情是这样的:

*   我们摆脱了回调地狱。
*   我们返回了一个 Promise 对象，它将处理函数的解析(或失败)。
*   我们使用了 [`then`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Objets_globaux/Promise/then) 操作符，并提供了一个回调函数来描述三秒钟后该做什么。
*   我们记录了...等一下...

## 回调？又来了？

是的。我们仍然需要使用回调，但是我们的代码现在比以前更易于维护和阅读。我相信我们都应该在这种工作中使用 Promise，特别是如果你是 Node.js 开发人员，并且仍然在核心模块中使用它们。你知道从 Node.js 的某个版本开始，你现在可以用这个:

```
const { promises: fs } = require("fs");

fs.readFile("...").then(function(data) { ... }); 
```

Enter fullscreen mode Exit fullscreen mode

而不是:

```
const fs = require("fs");

fs.readFile("...", function(error, data) { ... }); 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。在撰写本文时，它仍处于试验阶段，但我希望 Node.js 能够迎头赶上，在所有回调中使用 Promises！

这个话题到此结束。感谢您的关注...等等不要。我们可以做得更多。

## 但是`await`，还有更多...

从 ECMAScript 2017 开始，我们现在有权使用所有强大的`async` & `await`关键字，这进一步简化了 JavaScript 中异步指令的编写。所以回到我们之前的例子，我们现在可以使用这个语法:

```
"use strict";

// No more callback needed!
function sleep(seconds) {
  return new Promise(function(resolve) {
    setTimeout(function() {
      resolve();
    }, seconds * 1000);
  });
}

const SECONDS = 3;

async function main() {
  await sleep(SECONDS);

  console.log(`First call after ${SECONDS} seconds!`);

  await sleep(SECONDS);

  console.log(`Second call after ${SECONDS} seconds!`);

  await sleep(SECONDS);

  console.log(`Third call after ${SECONDS} seconds!`);
}

main(); 
```

Enter fullscreen mode Exit fullscreen mode

*[在线试用](https://repl.it/repls/DenseObviousHypothesis)*

发生了什么

*   我们取消所有的试镜。全部都是。
*   我们现在可以像同步一样编写代码了(但它不是！).
*   使用起来要简单得多。
*   我们添加了一个特殊的`await`关键字来等待承诺解决。

一个警告是，我们必须使用`async`关键字将所有的异步代码包装在一个异步函数中。但是我认为这是一个很小的代价来写这样一个剧本。我想用它而不是我们的第一个例子。

***好消息**:现在有一个新的用于服务器端 JavaScript 执行的运行时，名为 [Deno](https://github.com/denoland/deno) ，由 Node.js 平台的最初创建者之一编写。它支持现成的 TypeScript 和 JavaScript，据说还提供顶级异步支持，这意味着我们将能够摆脱那个`main`函数。是不是很酷？*

## 我们还能更进一步吗？

在我的研究过程中，我发现了一种优雅的方式来描述我的 JavaScript 代码中的延迟，这种方式利用了对象的[getter functionality](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get)(再次记住，一切都是对象，甚至是类！)并且可以这样使用:

```
"use strict";

const ONE_SECOND_IN_MILISECONDS = 1000;
const ONE_MINUTE_IN_SECONDS = 60;

class After {
  constructor(delay) {
    this.delay = delay;
  }

  get seconds() {
    return new Promise((resolve) => {
      setTimeout(resolve, this.delay * ONE_SECOND_IN_MILISECONDS);
    });
  }

  get minutes() {
    return new Promise(resolve => {
      setTimeout(resolve, this.delay * ONE_MINUTE_IN_SECONDS * ONE_SECOND_IN_MILISECONDS);
    });
  }
}

function after(delay) {
  return new After(delay);
}

async function main() {
  await after(3).seconds;

  console.log(`After 3 seconds!`);

  await after(0.1).minutes; // 6 seconds

  console.log(`After 6 seconds!`);
}

main(); 
```

Enter fullscreen mode Exit fullscreen mode

*[在线试用](https://repl.it/repls/PrevailingCandidOutliers)*

这里到底发生了什么？

*   我现在使用一个类来存储等待的延迟。
*   我还一个承诺，就像以前一样。
*   但是，我现在可以决定是否要等待几秒钟、几分钟等等，而不是几秒钟后再决定...
*   为`seconds`或`minutes`使用 getter 将不会返回一个简单的值，而是一个我们可以使用的承诺。
*   这就像用简单的英语交谈。

## 和...

这就是我在 JavaScript 中处理延迟的方式。我相信会有更好、更聪明的方法来做到这一点。JavaScript 是一种令人惊叹的语言，用途非常广泛，有太多的工具和范例可以用来编写。所以保持好奇心，坚持练习！

哦，如果你想以任何方式改进这篇文章，请便！错别字，错误，增强，大家在评论区讨论一下吧。

[![](img/2e09dc508bffa7990482c0cd3f794402.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XyfDjjJB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgflip.com/37z85r.jpg)
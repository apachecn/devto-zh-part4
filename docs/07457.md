# 关于 JavaScript 中的 async/await，你需要知道的 6 点

> 原文：<https://dev.to/yashints/6-points-you-need-to-know-about-async-await-in-javascript-1d1o>

如果您遇到过类似下面的代码，那么本文将从多方面帮助您😁。

```
fetchPizzas()
  .then((pizzas) =＞ {
    return sortByToppings(pizzas)
      .then((pizzas) =＞ {
        return checkDeliveryOptions(pizzas)
          .then((pizzasWithDelivery) =＞ {
            return checkBirthdayGift(pizzasWithDelivery)
              .then((pizza) =＞ {
                return sendToCustomer(pizza);
              });
          });
      });
  }); 
```

Enter fullscreen mode Exit fullscreen mode

## 一点背景

很多时候，我们有一堆任务要按顺序执行。例如，从文件处理到根据前一次调用的结果多次调用数据库。或者按顺序调用多个 API，其中一个调用依赖于另一个调用。

在引入`async/await`之前，许多人使用回调和`setTimeOut`来模拟他们想要的行为(又名回调地狱)。后来，人们开始使用承诺，这使得代码可读性更好，但当调用次数高时，它们会在同一个地方结束(又名承诺地狱)。

## 异步功能

JavaScript 中的一个函数是`async`，当它通过事件循环异步操作时，使用隐式承诺返回其结果。此外，其结果的类型应该是一个 [`AsyncFunction`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction) 对象。

这个功能不过是[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)和[发生器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators#Generator_functions)的组合。我不会详细讨论生成器，但是它们通常包含一个或多个关键字。

现在让我们看看`async`函数的运行情况。假设我们有一个返回字符串的函数:

```
function hi() {
  return 'Hi from JavaScript';
}

hi(); // 'Hi from JavaScript' 
```

Enter fullscreen mode Exit fullscreen mode

如果我们把`async`放在函数前面，那么它不再返回 string，它将是一个自动包装在 string 值周围的承诺。

```
async function hi() {
  return 'Hi from JavaScript';
}

hi(); // Promise {<resolved>: "Hi from JavaScript"} 
```

Enter fullscreen mode Exit fullscreen mode

现在为了从承诺中获得价值，我们像以前一样:

```
hi().then(console.log); // 'Hi from JavaScript' 
```

Enter fullscreen mode Exit fullscreen mode

你可能想知道这如何帮助解决承诺地狱。只是裸露与我，我们将有一步一步的例子，所以它会很清楚，当我们完成。

## 等待

`await`让 JavaScript 引擎等待，直到承诺被解决/拒绝，并返回结果。该关键字只能在`async`函数中使用。

```
const doSomething = async () => {
  console.log(await hi())
};

// 'Hi from JavaScript' 
```

Enter fullscreen mode Exit fullscreen mode

你可能会想，既然`await`迫使 JavaScript 引擎等待，它将会在 CPU 上有一些开销。但事实并非如此，因为引擎可以在等待承诺被解析/拒绝时执行其他脚本。另外，这比使用`promises`和`.then`要优雅得多。

**警告:**如果你试图在一个普通函数中使用`await`调用一个`async`函数，你会得到一个语法错误。

```
function doSomething() {
  await hi(); // Uncaught SyntaxError: await is only valid in async function
} 
```

Enter fullscreen mode Exit fullscreen mode

## 一个小抓手

大多数开始使用`async/await`的人忘记了他们不能在顶层代码上调用`async`函数。这是因为我们不能让`await`出现在一个普通的函数中，而顶级函数在默认情况下是普通的。

```
let response = await hi(); // syntax error in top-level code
console.log(response); 
```

Enter fullscreen mode Exit fullscreen mode

然而，你能做的是将你的代码包装在一个`async` `IIFE`(立即调用函数执行)中，并在那里调用它:

```
(async () => {
  let response = await hi(); 
  console.log(response); // 'Hi from JavaScript'
  ...
})(); 
```

Enter fullscreen mode Exit fullscreen mode

**更新**:正如[尼克·泰勒](https://dev.to/nickytonline)在评论中提到的，有一个在顶级代码中支持`await`的第 3 阶段提案。所以请继续关注这个空间:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ tc39 ](https://github.com/tc39) / [提案-顶层-等待](https://github.com/tc39/proposal-top-level-await)

### 顶级 ECMAScript“等待”提案(第 3 阶段)

<article class="markdown-body entry-content container-lg" itemprop="text">

# ECMAScript 提案:顶级`await`

冠军:迈尔斯·鲍恩斯

现状:第三阶段

## 摘要

顶级`await`使模块能够充当大型异步函数:有了顶级`await`，ECMAScript 模块(ESM)可以`await`资源，导致其他模块`import`它们在开始评估自己的身体之前等待。

## 动机

### 对 IIAFEs 的限制

由于`await`只在`async`函数中可用，通过将代码分解为`async`函数，模块可以在启动时执行的代码中包含一个`await`:

```
// awaiting.mjs
import { process } from "./some-module.mjs";
let output;
async function main() {
  const dynamic = await import(computedModuleSpecifier);
  const data = await fetch(url);
  output = process(dynamic.default, data);
}
main();
export { output };
```

Enter fullscreen mode Exit fullscreen mode

这种模式也可以立即调用。你可以称之为立即调用的异步函数…

</article>

[View on GitHub](https://github.com/tc39/proposal-top-level-await)

## 错误处理

我之前说过，大部分`async`函数都可以写成一个普通的带承诺的函数。然而，当涉及到错误处理时，`async`函数就不那么`error-prone`了。如果等待的调用失败，异常将被自动捕获，并且`Error`对象将使用隐式返回承诺传播给调用者。

在此之前，我们必须拒绝从正常函数返回的承诺，并在调用者中使用一个`.catch`。我见过很多地方，开发人员使用 try/catch 并抛出一个新的异常，这意味着堆栈跟踪将被重置。

```
async function hi() {
  throw new Error("Whoops!");
};

async function doSomething() {

  try {
    let response = await hi();
    return response;
  } catch(err) {    
    console.log(err);
  }
}

doSomething(); 
```

Enter fullscreen mode Exit fullscreen mode

或者你可以避开`try/catch`，因为调用`hi`产生的承诺被拒绝。然后简单地使用`.catch`来处理错误。

```
async function hi() {
  throw new Error("Whoops!");
};

async function doSomething() {
  let response = await hi();
  return response;
}

doSomething().catch(err => {
  console.log(err);
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果您认为更适合您的情况，您可以忽略全部捕获，使用[全局异常处理程序](https://developer.mozilla.org/en-US/docs/Web/API/WindowEventHandlers/onrejectionhandled)处理所有异常。使用了`WindowsEventHandlers` mixin 的`onrejectionhandled`属性。

```
window.onrejectionhandled = function(e) {
  console.log(e.reason);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 无极兼容

你可以用`async/await`和`Promise.all`一起等待多个承诺:

```
const responses = await Promise.all([
  fetch('yashints.dev/rss'),
  hi(),
  ...
]) 
```

Enter fullscreen mode Exit fullscreen mode

如果出现错误，它会像往常一样传播，从失败的承诺传播到`Promise.all`,然后变成一个异常，您可以使用上面的任何一种方法来捕捉这个异常。

## `await`可以取用一个“thenable”

类似于`promise.then`，如果你有任何对象有一个`.then`方法，`await`会接受它。这是为了支持这样的场景，其中第三方对象不是承诺，而是承诺兼容的(它支持`.then`)，与`await`一起使用就足够了。

```
class Greeting {
  constructor(name) {
    this.name = name;
  }

  then(resolve, reject) {
    console.log(resolve);

    setTimeout(() => resolve(`Hi ${this.name}`));
  }
};

async function greet() {
  const greeting = await Greeting('Yaser');

  console.log(greeting); // Hi Yaser
};

greet(); 
```

Enter fullscreen mode Exit fullscreen mode

## `async`类方法

你可以有一个`async`类方法。只要在它前面加上`async`就可以了。

```
class Order {
  async deliver() {
    return await Promise.resolve('Pizza');
  }
}

new Order()
  .deliver()
  .then(console.log); // Pizza 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

快速回顾一下我们到目前为止讨论的内容:

1.  `async`关键字使一个方法异步，反过来总是返回一个承诺并允许`await`被使用。
2.  `await`关键字 before a promise 让 JavaScript 一直等到解决/拒绝。如果承诺被拒绝，则生成异常，否则返回结果。
3.  总之，它们为我们编写干净的、更易测试的异步代码提供了很好的机会。
4.  有了`async/await`,你就不需要`.then/.catch`,但只要注意它们仍然是基于承诺的。
5.  您可以使用`Promise.all`来等待多个`async`函数调用。
6.  一个类中可以有一个`async`方法。

我知道围绕`async/await`有许多很棒的文章，但是我试图涵盖一些我必须不断提醒自己的项目。希望它能帮助你有一个集中的地方来存放你编写干净的异步 JavaScript 所需的大部分东西。

享受探索这些要点的乐趣。
# 让 JavaScript 更具功能性

> 原文：<https://dev.to/craigmichaelmartin/making-javascript-promises-more-functional-jp3>

[ *本文摘自[JavaScript](https://dev.to/craigmichaelmartin/the-problem-with-promises-in-javascript-5h46)中的承诺问题。这是最后一节，但考虑到它只是所列举问题的一种可能的解决办法，我认为它应该单独存在。在简短的介绍之后，它是未经编辑的，因为它是上述文章的最后一部分。* ]

几年前，我为一个节点后端 web 应用程序创建了一个新的 repo，并花了一些时间考虑如何在我的代码中处理承诺。在其他节点项目中，我已经开始看到一些关于承诺的重复出现的问题:当危险地使用时，API 似乎具有最好的人机工程学，他们缺乏一个方便的 API 来安全地处理数据，拒绝的承诺和意外的运行时异常混合在一起，留给开发人员去解决。

你可以在[JavaScript 中的承诺问题](https://dev.to/craigmichaelmartin/the-problem-with-promises-in-javascript-5h46)中读到更多关于这些问题的内容。

这篇文章是(这些问题的无数解决方案中的一个——可能是一个非常糟糕的)关于什么是解决方案的思想实验..哪家变成了图书馆: [fPromise](https://github.com/craigmichaelmartin/fpromise)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [克雷格·迈克尔·马丁](https://github.com/craigmichaelmartin) / [承诺](https://github.com/craigmichaelmartin/fpromise)

### 让承诺安全、方便、易读。

<article class="markdown-body entry-content container-lg" itemprop="text">

# `fPromise`

[![Build Status](img/56bd9ea7c6409d0d23f2bb22d3489209.png)](https://travis-ci.org/craigmichaelmartin/fpromise)[![Greenkeeper badge](img/f9aa3f1cece5095ec555f1ee368bbeac.png)](https://greenkeeper.io/)[![codecov](img/ab2c3f9666879a71d659958974074df5.png)](https://codecov.io/gh/craigmichaelmartin/fpromise)

## 装置

```
npm install --save fpromise
```

## 什么是`fPromise`？

`fPromise`是一个处理承诺的 javascript 库。

它试图用承诺解决三个问题:

*   承诺有一个 API，它鼓励偶然危险的代码
*   承诺将拒绝的承诺与非预期的本机异常混合在一起
*   承诺缺乏一套方便的 API 方法来处理结果

(关于这个库的背景和更好的解释，请阅读[那篇关于承诺问题的文章](https://dev.to/thecraigmichael/the-problem-with-promises-in-javascript-5h46)。

`fPromise`通过在 promises 中添加一个抽象层来解决这些问题——将 promises 的两个路径设计(已解决/已拒绝)重新设计为三个路径:一个数据路径、一个非本机异常路径(即，用于被您自己的意图拒绝的 promises)和一个本机异常路径。

有了这三条途径，我们就可以得到一个安全、方便、可读性更好的 API。

重要的是这种抽象:

*   利用承诺
*   保持 promise 原型不变
*   为…提供安全的 API

</article>

[View on GitHub](https://github.com/craigmichaelmartin/fpromise)

如果你还没有读过 JavaScript 中的[Problem with Promises，你可能想看看。](https://dev.to/craigmichaelmartin/the-problem-with-promises-in-javascript-5h46)

因此，让我们从一个关于更好的承诺可能看起来像什么的思想实验开始，看看我们是否能在用户域代码中实现它。我说的“更好”——是指对上述问题免疫。

# 一个“更好”的承诺实现会是什么样子？

感觉上`await`抛出本机异常是正确的(就像常规同步代码一样)。不理想的是，非本机错误在该桶中，因此必须被捕获，并且新的块范围降低了可读性，使代码更加脱节。

想象一下，如果 promises used rejected promises 仅用于本机运行时异常，并为数据/问题使用一个特殊的对象。让我们称这个特殊的对象为。它可迭代为两个元素的数组，数据作为第一个元素，issue 作为第二个元素。对于我们之前的观点，它还指定了像 map/imap(发布映射)和 tap/itap(发布 tap)这样的方法，它的两个实现(数据和发布)实现了这些方法。数据没有 imap 和 itap 的操作。问题没有映射和点击的操作。`map` / `imap`分别将结果重新包装为数据/问题，除非显式转换为其他。tap 方法是副作用，只有谁的回报没有被使用。

Promise.resolve 创建一个“常规的”承诺，将值包装在数据中。Promise.reject 创建一个“常规”承诺，如果拒绝不是一个本地错误，则包装问题中的值；否则，它会产生一个实际上“被拒绝”的承诺。

我们可以编写如下代码:

```
// Made up API below!

// data-access/user.js
const save = user => db.execute(user.getInsertSQL());
// As long as there is no native Exceptions, this returns a
// promise in the "regular" state.

// service/user.js
const save = data =>
  save(User(data))
    .tap(getStandardLog('user_creation'))   // Fictional
    .map(User.parseUserFromDB)              // Fictional
    .itap(logError);                        // Fictional

// controllers/user.js
const postHandler = async (userDate, response) => {
  // No need to use try/catch, as everything is in the "regular" state
  const [user, error] = await save(userData);  // Fictional
  if (error) {
    const errorToCode = { 'IntegrityError': 422 }; 
    return response.send(errorToCode[error.constructor.name] || 400);
  }
  response.send(204);
  postEmailToMailChimp(user.email).tapError(logError);
}; 
```

这种方法的特点:

*   被拒绝的承诺只用于本机异常，所以不需要使用 try/catch 块——可读性更好、更有凝聚力的代码。其他一切都在“常规”路径中，但作为数据或问题。
*   `map`、`tap`、`itap`将函数应用于“常规”路径承诺值的辅助实用程序。(请记住，map/tap 对错误无效，imap/itap 对数据无效。)
*   “regular”承诺使用数据或问题对数组进行值(数据|任一)析构(但是，再一次，决不会出现本机运行时错误——那些抛出的错误(这里可能会在 try/catch 中被捕获，但是没有人会为这种程度的恐惧而编程:例如`try { Math.random() } catch (err) { console.log('Just in case I typo-ed the string "Math" }`)
*   `await`允许我们留在调用堆栈中(允许返回)

对我来说，这感觉像是兑现了承诺。

## 我们能离上面的代码多近？

我们其实可以走得很近。

我们将

*   [x]使用承诺
*   [x]保持 promise 原型不变
*   [x]为使用它们提供一个安全的 API，它不是偶然危险的
*   [x]确保无意的运行时错误不被处理(因此在等待时抛出)
*   [x]提供处理数据的实用方法
*   [x]提高可读性/内聚性(与 try 块相比)
*   [x]在主调用块中保持控制(因此返回工作)

通过在 Promise 结构中提供一个安全的 API，我们将创建的这个“库”可以在 Promise 所在的任何地方使用，而不需要劫持原型或引入新的原语。

我们将创建一个任一类型来指定

*   `map`
*   `imap`
*   `tap`
*   `itap`
*   等等

并确保它对于两个元素的数组是可迭代的(可析构的)。

`Data`和`Issue`实现这个接口。

```
const Data = x => ({
  map: f => Data(f(x)),          // transform the data by applying the fn
  imap: f => Data(x),            // no-op (method targets Issue)
  bmap: (f, g) => Data(f(x)),    // run respective fn on data
  tap: f => (f(x), Data(x)),     // runs side effect fn on data
  itap: f => Data(x),            // no-op (method targets Issue)
  btap: (f, g) => (f(x), Data(x)),// run respective sideeffect fn on data
  val: () => [x],
  isData: true,
  isIssue: false,
  [Symbol.iterator]: function *() { yield x; }
});

const Issue = x => ({
  map: f => Issue(x),            // no-op (method targets Data)
  imap: f => Issue(f(x)),        // transform the issue by applyin the fn
  bmap: (f, g) => Issue(g(x)),   // run respective fn on issue
  tap: f => Issue(x),            // no-op (method target Data)
  itap: f => (f(x), Issue(x)),   // runs side effect fn on issue
  btap: (f, g) => (g(x), Issue(x)),//run respective sideeffect f on issue
  val: () => [, x],
  isData: false,
  isIssue: true,
  [Symbol.iterator]: function *() { yield void 0; yield x; }
}); 
```

我们需要一个`fp`来改变当前的承诺，按照我们的安全规则行事。

```
const ensureData = data =>
  data instanceof Data ? data : Data(data);

const nativeExceptions = [ EvalError, RangeError, ReferenceError, SyntaxError, TypeError, URIError ];

const ensureIssue = error => {
  if (error instanceof nativeException) {
    throw error;
  }
  return error instanceof Error ? error : Error(error);
};

const fp = promise => promise.then(ensureData, ensureIssue); 
```

为了使这些功能更强大，我们还可以添加:

```
const map = f => [o => ensureData(o).map(f), o => ensureIssue(o).map(f)];
const imap = f => [o => ensureData(o).imap(f), o => ensureIssue(o).imap(f)];
const bmap = (f, g) => [o => ensureData(o).bmap(f, g), o => ensureIssue(o).bmap(f, g)];
const tap = f => [o => ensureData(o).tap(f), o => ensureIssue(o).tap(f)];
const itap = f => [o => ensureData(o).itap(f), o => ensureIssue(o).itap(f)];
const btap = (f, g) => [o => ensureData(o).btap(f, g), o => ensureIssue(o).btap(f, g)]; 
```

改写上面虚构的 promise 代码，非常简单。我们:

1.  用一个`fp`来包装最初的承诺，让承诺遵守我们的规则(同样，它仍然是一个完全常规的承诺)。
2.  (等待承诺)在我们调用我们的工具方法之前。这是因为我们的实用方法要么是承诺解决的，要么是承诺本身。就上面这一点而言，我们并没有触及/修改承诺，只是在承诺之上加了一层。

```
// data-access/user.js
const save = user => fp(db.execute(user.getInsertSQL()))

// service/user.js
const save = async data =>
  (await save(User(data)))
    .tap(getStandardLog('user_creation))
    .map(User.parseUserFromDB)
    .itap(logError)

// controllers/user.js
const postHandler = async (userDate, response) => {
  const [user, error] = await save(userData);
  // ...
} 
```

如果我们想使用功能性更强的方法，不需要一开始就包装承诺:

```
// data-access/user.js
const save = user => db.execute(user.getInsertSQL();

// service/user.js
const save = data => save(data)
  .then(...tap(getStandardLog('user_creation)))
  .then(...map(User.parseUserFromDB))
  .then(...itap(logError))

// controllers/user.js
const postHandler = async (userDate, response) => {
  const [user, error] = await save(userData);
  // ...
} 
```

请注意，这两个条件都满足。我们是:

*   [x]利用承诺
*   [x]保持 promise 原型不变
*   [x]为使用它们提供一个安全的 API，它不是偶然危险的
*   [x]确保不会处理无意的运行时错误
*   [x]提供了处理数据的实用方法
*   [x]增加可读性(与 try 块相比)
*   [x]在主调用块中保持控制(因此返回工作)

如果我们想在功能方向上更进一步，我们可以:

```
// data-access/user.js
const save = user => db.execute(user.getInsertSQL();

// service/user.js
const save = data => save(data)
  .then(...tap(getStandardLog('user_creation')))
  .then(...map(User.parseUserFromDB))
  .then(...itap(logError))

// controllers/user.js
const postHandler = (userDate, response) =>
  save(userData).then(...map(
    user => //...
    error => //...
  ); 
```

如果你对这个创意感兴趣，[请在 github](https://github.com/craigmichaelmartin/fpromise) 上帮忙

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [克雷格·迈克尔·马丁](https://github.com/craigmichaelmartin) / [承诺](https://github.com/craigmichaelmartin/fpromise)

### 让承诺安全、方便、易读。

<article class="markdown-body entry-content container-lg" itemprop="text">

# `fPromise`

[![Build Status](img/56bd9ea7c6409d0d23f2bb22d3489209.png)](https://travis-ci.org/craigmichaelmartin/fpromise)[![Greenkeeper badge](img/f9aa3f1cece5095ec555f1ee368bbeac.png)](https://greenkeeper.io/)[![codecov](img/ab2c3f9666879a71d659958974074df5.png)](https://codecov.io/gh/craigmichaelmartin/fpromise)

## 装置

```
npm install --save fpromise
```

## 什么是`fPromise`？

`fPromise`是一个处理承诺的 javascript 库。

它试图用承诺解决三个问题:

*   承诺有一个 API，它鼓励偶然危险的代码
*   承诺将拒绝的承诺与非预期的本机异常混合在一起
*   承诺缺乏一套方便的 API 方法来处理结果

(关于这个库的背景和更好的解释，请阅读[那篇关于承诺问题的文章](https://dev.to/thecraigmichael/the-problem-with-promises-in-javascript-5h46)。

`fPromise`通过在 promises 中添加一个抽象层来解决这些问题——将 promises 的两个路径设计(已解决/已拒绝)重新设计为三个路径:一个数据路径、一个非本机异常路径(即，用于被您自己的意图拒绝的 promises)和一个本机异常路径。

有了这三条途径，我们就可以得到一个安全、方便、可读性更好的 API。

重要的是这种抽象:

*   利用承诺
*   保持 promise 原型不变
*   为…提供安全的 API

</article>

[View on GitHub](https://github.com/craigmichaelmartin/fpromise)

或者看看类似的-

# *其实这个空间里好的*项目

*   [https://gist . github . com/David wells/56089265 ab 613 a1 f 29 eabca 9 fc 68 a3 c 6](https://gist.github.com/DavidWells/56089265ab613a1f29eabca9fc68a3c6)
*   [https://github.com/gunar/go-for-it](https://github.com/gunar/go-for-it)
*   [https://github.com/majgis/catchify](https://github.com/majgis/catchify)
*   [https://github.com/scopsy/await-to-js](https://github.com/scopsy/await-to-js)
*   [https://github . com/flore-js/flore](https://github.com/fluture-js/Fluture)
*   [https://github.com/russellmcc/fantasydo](https://github.com/russellmcc/fantasydo)

# 关于这种东西的文章来自聪明人:

*   [https://medium . com/@ gunar/async-control-flow-without-exceptions-nor-monads-b 19 af 2 ACC 553](https://medium.com/@gunar/async-control-flow-without-exceptions-nor-monads-b19af2acc553)
*   [https://blog . Grossman . io/how-to-write-async-await-without-try-catch-blocks-in-JavaScript/](https://blog.grossman.io/how-to-write-async-await-without-try-catch-blocks-in-javascript/)
*   [http://Jesse warden . com/2017/11/easy-error-handling-using-asyncawait . html](http://jessewarden.com/2017/11/easier-error-handling-using-asyncawait.html)
*   [https://medium . freecodecamp . org/avoiding-the-async-await-hell-c 77 a 0 FB 71 c 4c](https://medium.freecodecamp.org/avoiding-the-async-await-hell-c77a0fb71c4c)
*   [https://medium . com/@ Dominic . mayers/async-await-without-promises-725 e 15 E1 b 639](https://medium.com/@dominic.mayers/async-await-without-promises-725e15e1b639)
*   [https://medium . com/@ Dominic . mayers/on-one-hand-the-async-await-framework-avoid-the-use-of-callbacks-to-define-the-main-flow-in-812317d 19285](https://medium.com/@dominic.mayers/on-one-hand-the-async-await-framework-avoid-the-use-of-callbacks-to-define-the-main-flow-in-812317d19285)
*   [https://dev . to/sadarshannayinar/capture-error-and-data-in-async-await-without-try-catch-1no 2](https://dev.to/sadarshannaiynar/capture-error-and-data-in-async-await-without-try-catch-1no2)
*   [https://medium . com/@ pyro listic/the-hard-error-handling-case-made-easy-with-async-await-597 FD 4b 908 B1](https://medium.com/@pyrolistical/the-hard-error-handling-case-made-easy-with-async-await-597fd4b908b1)
*   [https://gist . github . com/wou dsma/Fe 8598 B1 f 41453208 f 0661 f 90 ecdb 98 b](https://gist.github.com/woudsma/fe8598b1f41453208f0661f90ecdb98b)
# 让 wait 在 JavaScript 中更加实用

> 原文：<https://dev.to/craigmichaelmartin/making-await-more-functional-in-javascript-2le4>

在[Javascript 中承诺的问题](https://dev.to/craigmichaelmartin/the-problem-with-promises-in-javascript-5h46)中，我看到 API 和承诺的设计对于编写负责任和安全的代码来说是多么的危险。

我[包括了一个章节](https://dev.to/craigmichaelmartin/making-javascript-promises-more-functional-jp3)提出了一个库( [fPromise](https://github.com/craigmichaelmartin/fpromise) )，它使用了一种功能方法来克服这些问题。

在这篇文章发表后， [Mike Sherov](https://twitter.com/mikesherov) 很友好地回复了一条关于这篇文章的推文，并提供了他对这篇文章的看法:我低估了 async/async 语法的价值(它抽象出了棘手的 then/catch API，并让我们回到了“正常”的流程)，剩下的问题(即糟糕的错误处理)是 JavaScript 本身的问题(TC39 一直在发展)。

我非常感谢他在这方面的想法，并帮助阐明我提出的一个反叙事！！

迈克是这样说的:

> ![Mike Sherov 🚀 profile image](img/082e923e4cca8a6bf4e2d6fcf9d3c71d.png)麦克·谢罗夫🚀[@ mikesherov](https://dev.to/mikesherov)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ thecraigmichael](https://twitter.com/thecraigmichael)[@ eggheadio](https://twitter.com/eggheadio)[@ mariusschulz](https://twitter.com/mariusschulz)“承诺有一个 API，鼓励随便写危险代码。”一旦涉及到 async/await，我不同意文章中的例子。try/catch 是一个常见的范例，事实上，可选的 catch 绑定是在零错误检查的情况下使用的...这才是真正的足球枪。2019 年 8 月 21 日下午 13:37[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1164169660973092864)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1164169660973092864)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1164169660973092864)0

> ![Mike Sherov 🚀 profile image](img/082e923e4cca8a6bf4e2d6fcf9d3c71d.png)麦克·谢罗夫🚀[@ mikesherov](https://dev.to/mikesherov)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ thecraigmichael](https://twitter.com/thecraigmichael)[@ eggheadio](https://twitter.com/eggheadio)[@ mariusschulz](https://twitter.com/mariusschulz)我想我的一般观点是，既然 async/await 恢复了我们看起来正常的 try/catch 同步代码，那么您的所有观点都可以归结为 JS 的其他特性的问题:-)2014:21pm-21 Aug

> ![Mike Sherov 🚀 profile image](img/082e923e4cca8a6bf4e2d6fcf9d3c71d.png)麦克·谢罗夫🚀[@ mikesherov](https://dev.to/mikesherov)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ thecraigmichael](https://twitter.com/thecraigmichael)[@ eggheadio](https://twitter.com/eggheadio)[@ mariusschulz](https://twitter.com/mariusschulz)或许我们可以将 JS 缺乏基于类型的多个 catch 块，以及语言中普遍缺乏类型，作为真正的问题。但我个人不会责怪承诺。2019 年 8 月 21 日下午 13:40[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1164170411044679682)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1164170411044679682)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1164170411044679682)0

让我们看看问题文章中的一个例子:

```
const handleSave = async rawUserData => {
  try {
    const user = await saveUser(rawUserData);
    createToast(`User ${displayName(user)} has been created`);
  } catch {
    createToast(`User could not be saved`));
  }
}; 
```

我对此犹豫不决，因为尝试“捕捉”了太多，并且使用了这样一个点:如果`displayName`抛出，用户将被警告没有用户被保存，即使它被保存了。但是——尽管代码有点单调——这是可以克服的——并且没有显示出来对我来说是一件坏事。

如果我们的 catch 在错误处理方面很聪明，这种情况就会消失。

```
const handleSave = async rawUserData => {
  try {
    const user = await saveUser(rawUserData);
    createToast(`User ${displayName(user)} has been created`);
  } catch (err) {
    if (err instanceof HTTPError) {
      createToast(`User could not be saved`));
    } else {
      throw err;
    }
  }
}; 
```

如果语言的发展包括更好的错误处理，这种方法会感觉更好:

```
// (code includes fictitious catch handling by error type)
const handleSave = async rawUserData => {
  try {
    const user = await saveUser(rawUserData);
    createToast(`User ${displayName(user)} has been created`);
  } catch (HTTPError as err) {
    createToast(`User could not be saved`));
  }
}; 
```

虽然这要好得多，但我仍然不愿意尝试太多。我相信 catch 的*应该*只捕捉他们想要的异常(我在最初的帖子中做得不好)，但是“尝试”的范围应该尽可能小。

否则，随着代码的增长，会出现捕捉冲突:

```
// (code includes fictitious catch handling by error type)
const handleSave = async rawUserData => {
  try {
    const user = await saveUser(rawUserData);
    createToast(`User ${displayName(user)} has been created`);
    const mailChimpId = await postUserToMailChimp(user);
  } catch (HTTPError as err) {
    createToast(`Um...`));
  }
}; 
```

所以这里有一个关于我们正在捕捉什么的更狭隘的方法:

```
// (code includes fictitious catch handling by error type)
const handleSave = async rawUserData => {
  try {
    const user = await saveUser(rawUserData);
    createToast(`User ${displayName(user)} has been created`);
    try {
        const mailChimpId = await postUserToMailChimp(user);
        createToast(`User ${displayName(user)} has been subscribed`);
    } catch (HTTPError as err) {
        createToast(`User could not be subscribed to mailing list`));
    }
  } catch (HTTPError as err) {
    createToast(`User could not be saved`));
  }
}; 
```

但现在我们发现自己陷入了一个“地狱”的尝试/捕捉区。让我们试着摆脱它:

```
// (code includes fictitious catch handling by error type)
const handleSave = async rawUserData => {
  let user;
  try {
    user = await saveUser(rawUserData);
  } catch (HTTPError as err) {
    createToast(`User could not be saved`));
  }
  if (!user) {
    return;
  }
  createToast(`User ${displayName(user)} has been created`);

  let mailChimpId;
  try {
    await postUserToMailChimp(rawUserData);
  } catch (HTTPError as err) {
    createToast(`User could not be subscribed to mailing list`));
  }
  if (!mailChimpId) {
    return;
  }
  createToast(`User ${displayName(user)} has been subscribed`);
}; 
```

尽管这是负责任和安全的代码，但它看起来最不可读，就像我们正在做一些错误和丑陋的事情，并努力反对这种语言。此外，请记住，这段代码使用了一个简洁的虚构错误处理程序，而不是检查错误类型并处理其他重新抛出错误的更冗长(真实)的代码。

这(我相信)正是 Mike 的观点，即错误处理(一般而言)需要改进，也正是我的观点——做带有承诺的异步代码是很危险的，因为它使危险的代码变得干净和符合人体工程学，而负责任的代码可读性和直观性更差。

那么，还有什么比这更好的呢？如果有-

## 等待捕获处理

如果我们能做这样的事情呢？

```
// (code includes fictitious await catch handling by error type)
const handleSave = async rawUserData => {
  const [user, httpError] = await saveUser(rawUserData) | HTTPError;
  if (httpError) {
    return createToast(`User could not be saved`));
  }
  createToast(`User ${displayName(user)} has been created`);

  const [id, httpError] = await saveUser(rawUserData) | HTTPError;
  if (httpError) {
    return createToast(`User could not be subscribed to mailing list`));
  }
  createToast(`User ${displayName(user)} has been subscribed`);
}; 
```

这读起来很好，是安全和负责任的！我们正在捕捉我们想要的错误类型。任何其他错误都会导致 await“抛出”。

并且它可以用于多种错误类型。例如，

```
// (code includes fictitious catch handling by error type)
const [user, foo, bar] = await saveUser(rawUserData) | FooError, BarThing; 
```

## 在 userland 中我们能离这个多近？

相当接近。介绍 [fAwait](https://github.com/craigmichaelmartin/fawait) (如 functional-await)。

```
const {fa} = require('fawait');
const [user, httpError] = await fa(saveUser(rawUserData), HTTPError);
const [user, foo, bar] = await fa(saveUser(rawUserData), FooError, BarThing); 
```

感谢阅读！

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [克雷格·迈克尔·马丁](https://github.com/craigmichaelmartin) / [法威特](https://github.com/craigmichaelmartin/fawait)

### 一个 javascript 库，使 await 功能更加强大

<article class="markdown-body entry-content container-lg" itemprop="text">

# `fAwait`

[![codecov](img/67bc5272580fc50a7bf2ac3406c3c3ba.png)](https://codecov.io/gh/craigmichaelmartin/fawait)[![Build Status](img/1f984a8ed2b256b05690106255547e4c.png)](https://travis-ci.org/craigmichaelmartin/fawait)[![Greenkeeper badge](img/faff72847af982c3cd250a657aadb3ac.png)](https://greenkeeper.io/)

## 装置

```
npm install --save fawait
```

## 什么是`fAwait`？

`fAwait`是一个 javascript 库，用于处理承诺的`await`语法。

将您的承诺包装在`fa`函数中，并提供您想要捕获的错误，您将收到一个数组，您可以将它解包为这些值。任何未指定的错误都将被抛出。

阅读相关内容:[让等待在 JavaScript 中更加实用](https://dev.to/craigmichaelmartin/making-await-more-functional-in-javascript-2le4)

```
let [data, typeError, customBadThing] = await fa(promise, TypeError, BadThing);
```

## 替代品/现有技术

*   [`fPromise`](https://github.com/craigmichaelmartin/fpromise) 这是一个比较重的许诺方案。
*   [`go-for-it`](https://github.com/gunar/go-for-it) 和 [`safe-await`](https://github.com/DavidWells/safe-await) 将所有非母语错误转换成该函数形式。
*   [`await-to-js`](https://github.com/scopsy/await-to-js) 即把所有误差转换成这种函数形式。

</article>

[View on GitHub](https://github.com/craigmichaelmartin/fawait)
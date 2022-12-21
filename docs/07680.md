# 更好的异步/等待错误处理

> 原文：<https://dev.to/sobiodarlington/better-error-handling-with-async-await-2e5m>

本文旨在建议一种在使用 async/await 语法时处理错误的更好方法。事先了解承诺是如何起作用的很重要。

## 从回调地狱到承诺

> 回调地狱(Callback Hell)，也称为末日金字塔(Pyramid of Doom)，是一种反模式，出现在不擅长异步编程的程序员的代码中。——科林·托

[回调地狱](http://callbackhell.com/)由于回调函数的多重嵌套，使你的代码向右漂移而不是向下。

我不会深入讨论什么是[回调地狱](http://callbackhell.com/)的细节，但是我会给出一个它看起来如何的例子。

#### 用户简介示例 1

```
// Code that reads from left to right 
// instead of top to bottom

let user;
let friendsOfUser;

getUser(userId, function(data) {
  user = data;

  getFriendsOfUser(userId, function(friends) {
    friendsOfUser = friends;

    getUsersPosts(userId, function(posts) {
      showUserProfilePage(user, friendsOfUser, posts, function() {
        // Do something here

      });
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 承诺

Javascript(ES6)语言中引入了承诺(T1 ),以更好地处理异步操作，而不会变成 T2 回调地狱(T3)。

下面的例子通过使用多个链接的`.then`调用而不是嵌套回调来解决回调问题。

#### 用户简介示例 2

```
// A solution with promises

let user;
let friendsOfUser;

getUser().then(data => {
  user = data;

  return getFriendsOfUser(userId);
}).then(friends => {
  friendsOfUser = friends;

  return getUsersPosts(userId);
}).then(posts => {
  showUserProfilePage(user, friendsOfUser, posts);
}).catch(e => console.log(e)); 
```

Enter fullscreen mode Exit fullscreen mode

promise 的解决方案看起来更干净，可读性更好。

## 承诺与异步/等待

[Async/await](https://javascript.info/async-await) 是一种特殊的语法，以更简洁的方式处理承诺。
在任何`function`之前添加 async 将函数变成承诺。

> 所有的`async`函数都返回承诺。

#### 举例

```
// Arithmetic addition function
async function add(a, b) {
  return a + b;
}

// Usage: 
add(1, 3).then(result => console.log(result));

// Prints: 4 
```

Enter fullscreen mode Exit fullscreen mode

使用 async/await 让`User profile example 2`看起来更好

#### 用户简介示例 3

```
async function userProfile() {
  let user = await getUser();
  let friendsOfUser = await getFriendsOfUser(userId);
  let posts = await getUsersPosts(userId);

  showUserProfilePage(user, friendsOfUser, posts);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 等等！有一个问题

如果`User profile example 3`中的任何一个请求出现拒绝承诺的情况，就会抛出`Unhandled promise rejection`异常。

在此之前，拒绝承诺不会引发错误。带有未处理拒绝的承诺过去常常无声无息地失败，这可能使调试成为一场噩梦。

谢天谢地，承诺现在被拒绝时抛出。

*   **谷歌 Chrome 抛出:** `VM664:1 Uncaught (in promise) Error`

*   **节点会抛出类似**的东西:`(node:4796) UnhandledPromiseRejectionWarning: Unhandled promise rejection (r ejection id: 1): Error: spawn cmd ENOENT
    [1] (node:4796) DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.`

> 任何承诺都不应该错过。- *Javascript*

注意`User profile example 2`中的`.catch`方法。
没有了。当承诺被拒绝时，catch 块 Javascript 将抛出`Unhandled promise rejection`错误。

在`User profile example 3`中解决这个问题很容易。`Unhandled promise rejection`可以通过在尝试中包装 await 操作来防止错误...捕捉块:

#### 用户简介示例 4

```
async function userProfile() {
  try {
    let user = await getUser();
    let friendsOfUser = await getFriendsOfUser(userId);
    let posts = await getUsersPosts(userId);

    showUserProfilePage(user, friendsOfUser, posts);
  } catch(e) {
    console.log(e);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*问题解决了！*

### ...但是错误处理可以改进

*你怎么知道有错误的是来自哪个异步请求？*

我们可以对异步请求调用一个`.catch`方法来处理错误。

#### 用户简介示例 5

```
let user = await getUser().catch(e => console.log('Error: ', e.message));

let friendsOfUser = await getFriendsOfUser(userId).catch(e => console.log('Error: ', e.message));

let posts = await getUsersPosts(userId).catch(e => console.log('Error: ', e.message));

showUserProfilePage(user, friendsOfUser, posts); 
```

Enter fullscreen mode Exit fullscreen mode

上面的解决方案将处理来自请求的单个错误，但是它是模式的混合。应该有一种更干净的方法来使用 async/await 而不使用`.catch`方法(如果你不介意的话，你可以这样做)。

## 这是我对更好的异步/等待错误处理的解决方案

#### 用户简介示例 6

```
/**
 * @description ### Returns Go / Lua like responses(data, err) 
 * when used with await
 *
 * - Example response [ data, undefined ]
 * - Example response [ undefined, Error ]
 *
 *
 * When used with Promise.all([req1, req2, req3])
 * - Example response [ [data1, data2, data3], undefined ]
 * - Example response [ undefined, Error ]
 *
 *
 * When used with Promise.race([req1, req2, req3])
 * - Example response [ data, undefined ]
 * - Example response [ undefined, Error ]
 *
 * @param {Promise} promise
 * @returns {Promise} [ data, undefined ]
 * @returns {Promise} [ undefined, Error ]
 */
const handle = (promise) => {
  return promise
    .then(data => ([data, undefined]))
    .catch(error => Promise.resolve([undefined, error]));
}

async function userProfile() {
  let [user, userErr] = await handle(getUser());

  if(userErr) throw new Error('Could not fetch user details');

  let [friendsOfUser, friendErr] = await handle(
    getFriendsOfUser(userId)
  );

  if(friendErr) throw new Error('Could not fetch user\'s friends');

  let [posts, postErr] = await handle(getUsersPosts(userId));

  if(postErr) throw new Error('Could not fetch user\'s posts');

  showUserProfilePage(user, friendsOfUser, posts);
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`handle`效用函数，我们能够避免`Unhandled promise rejection`错误，也能精确地处理错误。

### 解释

`handle`实用函数将一个承诺作为参数，并总是解析它，返回一个带有`[data|undefined, Error|undefined]`的数组。

*   如果传递给`handle`函数的承诺被解析，它将返回`[data, undefined]`；
*   如果它被拒绝，`handle`函数仍然解析它并返回`[undefined, Error]`

## 类似方案

*   [使用异步/等待进行更简单的错误处理](https://medium.com/@jesterxl/easier-error-handling-using-async-await-b9ab0cb938e) - [杰西·沃顿](https://twitter.com/jesterxl)
*   [NPM 包装等待订单](https://www.npmjs.com/package/await-to-js)

## 结论

Async/await 有一个清晰的语法，但是您仍然必须处理异步函数中抛出的异常。

除非实现定制的错误类，否则处理 promise `.then`链中的`.catch`错误会很困难。

使用`handle`效用函数，我们能够避免`Unhandled promise rejection`错误，也能精确地处理错误。
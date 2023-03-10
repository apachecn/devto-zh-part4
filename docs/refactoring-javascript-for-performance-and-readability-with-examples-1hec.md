# 重构 JavaScript 以提高性能和可读性(附示例！)

> 原文：<https://dev.to/healeycodes/refactoring-javascript-for-performance-and-readability-with-examples-1hec>

最近写了一篇关于[如何写非常快的 JavaScript](https://dev.to/healeycodes/solving-puzzles-with-high-performance-javascript-3o4k) 的文章。一些例子将它发挥到了极致，变得非常快，代价是完全不可维护。在速度和理解之间有一个中间地带，这就是*好代码*存在的地方。

我们将根据我遇到的真实例子重构一些代码。有时我需要在提交 PR 之前对我自己的代码进行这种重构。其他时候，我会在一个[故事](https://en.wikipedia.org/wiki/User_story)或 bug 的开头对现有代码做一个小的重构，让我的修改更容易实现。

## 场景 1

我们是一个网址缩短网站，就像 TinyURL 一样。我们接受一个长 URL，并返回一个短 URL，将访问者转发到长 URL。我们有两个功能。

```
// Unrefactored code

const URLstore = [];

function makeShort(URL) {
  const rndName = Math.random().toString(36).substring(2);
  URLstore.push({[rndName]: URL});
  return rndName;
}

function getLong(shortURL) {
  for (let i = 0; i < URLstore.length; i++) {
    if (URLstore[i].hasOwnProperty(shortURL) !== false) {
      return URLstore[i][shortURL];
    }
  }
} 
```

问题:如果用一个不在商店里的短 URL 调用`getLong`会发生什么？没有显式返回任何内容，因此将返回`undefined`。由于我们不确定如何处理这个问题，所以让我们明确地抛出一个错误，这样就可以在开发过程中发现问题。

就性能而言，如果你经常迭代一个平面数组，特别是当它是你程序的核心部分时，要小心。这里的重构是为了改变`URLstore`的数据结构。

目前，每个 URL 对象都存储在一个数组中。我们将把它想象成一排桶。当我们想将短 URL 转换成长 URL 时，平均来说，我们需要检查其中的一半，才能找到正确的短 URL。如果我们有成千上万个桶，并且每秒钟执行数百次，会怎么样？

答案是使用某种形式的[散列函数](https://en.wikipedia.org/wiki/Hash_function)，它在表面下映射和设置[使用。*哈希函数用于将给定的键映射到哈希表中的某个位置*。下面，当我们在`makeShort`中将我们的短 URL 放入商店，并在`getLong`中将其取出时，就会发生这种情况。根据您如何](https://v8.dev/blog/hash-code)[测量运行时间](https://en.wikipedia.org/wiki/Big_O_notation)，结果是*平均而言*我们只需要检查一个桶——不管总共有多少个桶！

```
// Refactored code

const URLstore = new Map(); // Change this to a Map

function makeShort(URL) {
  const rndName = Math.random().toString(36).substring(2);
  // Place the short URL into the Map as the key with the long URL as the value
  URLstore.set(rndName, URL);
  return rndName;
}

function getLong(shortURL) {
  // Leave the function early to avoid an unnecessary else statement
  if (URLstore.has(shortURL) === false) {
    throw 'Not in URLstore!';
  }
  return URLstore.get(shortURL); // Get the long URL out of the Map
} 
```

对于这些例子，我们假设随机函数不会冲突。“克隆 TinyURL”是一个常见的系统设计问题，也是一个非常有趣的问题。随机函数*和*冲突怎么办？很容易添加关于扩展和冗余的附录。

## 场景二

我们是一个社交媒体网站，用户网址是随机生成的。我们将使用 Glitch 团队开发的`friendly-words`包，而不是随机的胡言乱语。他们用这个为你最近创建的项目生成随机的名字！

```
// Unrefactored code

const friendlyWords = require('friendly-words');

function randomPredicate() {
  const choice = Math.floor(Math.random() * friendlyWords.predicates.length);
  return friendlyWords.predicates[choice];
}

function randomObject() {
  const choice = Math.floor(Math.random() * friendlyWords.objects.length);
  return friendlyWords.objects[choice];
}

async function createUser(email) {
  const user = { email: email };
  user.url = randomPredicate() + randomObject() + randomObject();
  await db.insert(user, 'Users')
  sendWelcomeEmail(user);
} 
```

人们常说一个函数应该做一件事。在这里，`createUser`做一件事..算是吧。它创建了一个用户。然而，如果我们展望未来，很有可能(如果我们的业务成功的话)这个功能会变得非常大。所以让我们早点开始，把它打破。

你可能已经注意到在我们的随机函数中有一些重复的逻辑。`friendly-worlds`包还提供了“团队”和“集合”的列表。我们不能为每个选项编写函数。让我们写一个接受友好事物列表的函数。

```
// Refactored code

const friendlyWords = require('friendly-words');

const generateURL = user => {
  const pick = arr => arr[Math.floor(Math.random() * arr.length)];
  user.url = `${pick(friendlyWords.predicates)}-${pick(friendlyWords.objects)}` +
    `-${pick(friendlyWords.objects)}`; // This line would've been too long for linters!
};

async function createUser(email) {
  const user = { email: email };
  // The URL-creation algorithm isn't important to this function so let's abstract it away
  generateURL(user);
  await db.insert(user, 'Users')
  sendWelcomeEmail(user);
} 
```

我们分离了一些逻辑，减少了代码行数。我们内联了一个名为`pick`的函数，它接受一个长度为 1 及以上的数组并返回一个随机选择，然后我们使用一个[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)来构建一个 URL。

## 策略

这里有一些简单的实现方法，可以使代码更容易阅读。当谈到干净的代码时，没有绝对的东西——总是有边缘情况！

从函数中提前返回:

```
function showProfile(user) {
  if (user.authenticated === true) {
    // ..
  }
}

// Refactor into ->

function showProfile(user) {
  // People often inline such checks
  if (user.authenticated === false) { return; }
  // Stay at the function indentation level, plus less brackets
} 
```

缓存变量以便函数可以像句子一样阅读:

```
function searchGroups(name) {
  for (let i = 0; i < continents.length; i++) {
    for (let j = 0; j < continents[i].length; j++) {
      for (let k = 0; k < continents[i][j].tags.length; k++) {
        if (continents[i][j].tags[k] === name) {
          return continents[i][j].id;
        }
      }
    }
  }
}

// Refactor into ->

function searchGroups(name) {
  for (let i = 0; i < continents.length; i++) {
    const group = continents[i]; // This code becomes self-documenting
    for (let j = 0; j < group.length; j++) {
      const tags = group[j].tags;
      for (let k = 0; k < tags.length; k++) {
        if (tags[k] === name) {
          return group[j].id; // The core of this nasty loop is clearer to read
        }
      }
    }
  }
} 
```

在实现自己的功能之前检查 Web API:

```
function cacheBust(url) {
  return url.includes('?') === true ?
    `${url}&time=${Date.now()}` :
    `${url}?time=${Date.now()}`
}

// Refactor into ->

function cacheBust(url) {
  // This throws an error on invalid URL which stops undefined behaviour
  const urlObj = new URL(url);
  urlObj.searchParams.append('time', Date.now); // Easier to skim read
  return url.toString();
} 
```

第一次就把代码做好是很重要的，因为在很多业务中，重构并没有太大的价值。或者至少，很难让涉众相信最终忽视代码库会使生产力停滞不前。

* * *

加入我的关于编程和个人成长的[时事通讯](https://buttondown.email/healeycodes)的 150 多人注册！

我发关于科技的微博。
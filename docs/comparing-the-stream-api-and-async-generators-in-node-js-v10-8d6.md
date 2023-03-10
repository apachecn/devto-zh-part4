# 比较 Node.js v10 中的流 API 和(异步)生成器

> 原文：<https://dev.to/bnevilleoneill/comparing-the-stream-api-and-async-generators-in-node-js-v10-8d6>

[![](img/95e736fd5965c4575b1d14669444f5dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wNF2BLfh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sbt8d6hmeq7l1uad573v.png)

## 简介

一个[流](https://en.wikipedia.org/wiki/Stream_(computing))是编程中数据的抽象。Node.js [流 API](https://nodejs.org/dist/latest-v10.x/docs/api/stream.html) 已经存在很久了，被用作读写异步数据的统一 API。Stream API 主要在内部与其他 API 一起使用，如`fs`和`http`。

例如，`fs.createReadStream`常用于读取大文件。另一个例子是`http.ServerResponse`，它实现了流 API，这样服务器就可以响应大数据。流主要用于大型数据，但从概念上讲，它可以表示无限长的数据。

还有另一种循环抽象，称为[生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)(在 ES2015 中引入)，类似于流。生成器返回一个迭代器，您可以在其中循环每一项，并且能够表示无限长的数据。ES2018 引入了异步生成器/迭代器，可以处理异步数据。在 [Node.js v10](https://node.green/#ES2018-features-Asynchronous-Iterators-async-generators) 中支持异步生成器。

在这篇文章中，我们将学习如何用基于拉的流和生成器实现同步计数器。我们还将使用基于推的流和异步生成器实现异步计数器，以便比较流 API 和异步生成器。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## 先决条件

在继续之前，读者需要安装[node . js](https://nodejs.org/en/download/)，并且[对 streams](https://nodejs.org/dist/latest-v10.x/docs/api/stream.html) 有一个基本的了解。

## 为同步计数器实现一个流

一般来说，你可以使用一个库提供的流，换句话说，你使用一个流。现在出于学习的目的，我们自己提供一个流。文档描述了如何实现流。让我们首先将一个无限计数器作为一个可读的流。创建一个文件，命名为“stream-sync-counter.js”。

```
// stream-sync-counter.js

const { Readable, Writable } = require('stream');

const createCounterReader = () => {
  let count = 0;
  return new Readable({
    objectMode: true,
    read() {
      count += 1;
      console.log('reading:', count);
      this.push(count);
    },
  });
};

const counterReader = createCounterReader(); 
```

Enter fullscreen mode Exit fullscreen mode

这是一个基于拉的流，这意味着如果缓冲区低于一定量，它将读取新的值。我们使用了“[对象模式](https://nodejs.org/dist/latest-v10.x/docs/api/stream.html#stream_object_mode)，所以项目只是一个数字。

现在，让我们定义一个可写的流来使用这个计数器。

```
// stream-sync-counter.js (continued)

const logWriter = new Writable({
  objectMode: true,
  write: (chunk, _, done) => {
    console.log('writing:', chunk);
    done();
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们上面定义的函数`logWriter`除了向控制台输出数字之外什么也不做。

现在，我们连接这些溪流，也称为“管道”

```
// stream-sync-counter.js (continued)

counterReader.pipe(logWriter); 
```

Enter fullscreen mode Exit fullscreen mode

如果你运行这段代码，你会看到数字无限递增。

```
$ node stream-sync-counter.js
reading: 1
reading: 2
writing: 1
reading: 3
writing: 2
reading: 4
writing: 3
reading: 5
writing: 4
reading: 6
writing: 5
... 
```

Enter fullscreen mode Exit fullscreen mode

需要注意的是，可读流一次读取几个项目来填充它的缓冲区，并等待直到一些项目被消费掉。readable stream 的工作方式是 1)读取项目并将它们存储在缓冲区中，2)等待直到项目被消耗，3)如果一些项目被消耗并且缓冲区变空(=“低于某个量”)，则返回到步骤 1)。为了更好地了解缓冲区是如何工作的，可以在可写流中设置超时。

```
// modify the function in stream-sync-counter.js

const logWriter = new Writable({
  objectMode: true,
  write: (chunk, _, done) => {
    setTimeout(() => {
      console.log('writing:', chunk);
      done();
    }, 100);
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果你运行它，你会看到一个有趣的输出:

```
$ node stream-sync-counter.js
reading: 1
reading: 2
reading: 3
...
reading: 31
reading: 32
writing: 1
writing: 2
writing: 3
...
writing: 14
writing: 15
writing: 16
reading: 33
reading: 34
reading: 35
...
reading: 46
reading: 47
reading: 48
writing: 17
... 
```

Enter fullscreen mode Exit fullscreen mode

## 实现一个生成器为同步计数器

[发生器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)是 ES2015 中引入的一项功能。它是循环的一般抽象，允许将循环实现为一个函数。生成器是一个返回迭代器的特殊函数。

下面是生成无限计数器的代码。创建一个文件，命名为“generator-sync-counter.js”。

```
// generator-sync-counter.js

function* counterGenerator() {
  let count = 0;
  while (true) {
    count += 1;
    console.log('reading:', count);
    yield count;

}

const counterIterator = counterGenerator(); 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们创建一个函数来运行这个迭代器并将数字输出到控制台。

```
// generator-sync-counter.js (continued)

const logIterator = (iterator) => {
  for (const item of iterator) {
    console.log('writing:', item);

}; 
```

Enter fullscreen mode Exit fullscreen mode

这只是一个 for-of 循环。在 ES2015 中，您可以简单地使用 for-of 循环来循环迭代器。您可以简单地调用该函数。

```
// generator-sync-counter.js (continued)

logIterator(counterIterator); 
```

Enter fullscreen mode Exit fullscreen mode

结果看起来会像这样:

```
$ node generator-sync-counter.js
reading: 1
writing: 1
reading: 2
writing: 2
reading: 3
writing: 3
reading: 4
writing: 4
reading: 5
writing: 5
reading: 6
writing: 6 
```

Enter fullscreen mode Exit fullscreen mode

这与流的行为略有不同，并且更直观，因为没有缓冲区。

您也可以像这样添加超时:

```
// modify the function in generator-sync-counter.js

const logIterator = async (iterator) => {
  for (const item of iterator) {
    await new Promise(r => setTimeout(r, 100));
    console.log('writing:', item);

}; 
```

Enter fullscreen mode Exit fullscreen mode

如果你运行它，你应该得到同样的结果。

我们基本上已经创建了一个同步的无限计数器，既有一个流又有一个生成器。它与我们使用计数器时的工作方式相同，但是内部行为略有不同，因为流是缓冲的。

## 为异步计数器实现一个流

接下来，我们将首先用一个流创建一个异步计数器。这里的异步计数器意味着它将每秒计数一次。为了创建这样的流，我们使用 setInterval。创建一个文件，命名为“stream-async-counter.js”。

```
// stream-async-counter.js

const { Readable, Writable } = require('stream');

const createCounterReader = (delay) => {
  let counter = 0;
  const reader = new Readable({
    objectMode: true,
    read() {},
  });
  setInterval(() => {
    counter += 1;
    console.log('reading:', counter);
    reader.push(counter);
  }, delay);
  return reader;
};

const counterReader = createCounterReader(1000); 
```

Enter fullscreen mode Exit fullscreen mode

这就是所谓的基于推送的流。正如您可能猜到的那样，它会无限期地将数据推入缓冲区，除非您消耗数据的速度比推入的速度快。

我们使用没有超时的`logWriter`,因为项目是从可读流中推出的，它控制计时。

```
// stream-async-counter.js (continued)

const logWriter = new Writable({
  objectMode: true,
  write: (chunk, _, done) => {
    console.log('writing:', chunk);
    done();
  },
});

counterReader.pipe(logWriter); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们运行这个，我们应该会看到下面的结果**和延迟**。

```
$ node stream-async-counter.js
reading: 1
writing: 1
reading: 2
writing: 2
reading: 3
writing: 3
reading: 4
writing: 4
reading: 5
writing: 5
... 
```

Enter fullscreen mode Exit fullscreen mode

这与基于拉的流的结果略有不同，因为现在我们在新项被添加到缓冲区之前消耗数据。

要查看数据是否被推送而不管是否被消费，可以按如下方式更改日志编写器。

```
// modify the function in stream-async-counter.js

const logWriter = new Writable({
  objectMode: true,
  write: (chunk, _, done) => {
    setTimeout(() => {
      console.log('writing:', chunk);
      done();
    }, 5 * 1000);
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 对异步计数器使用异步发生器

的 [for-await-of 是 ES2018 中的新功能。它允许在迭代器中处理承诺。使用异步生成器，我们可以定义一个类似于上一节中的异步无限计数器。创建一个名为“生成器-异步-计数器. js”的文件:](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for-await...of) 

```
// generator-async-counter.js

async function* counterGenerator(delay) {
  let counter = 0;
  while (true) {
    await new Promise(r => setTimeout(r, delay));
    counter += 1;
    console.log('reading:', counter);
    yield counter;

} 

const counterIterator = counterGenerator(1000); 
```

Enter fullscreen mode Exit fullscreen mode

注意，在上面显示的代码中，我们使用`Promise`来等待一秒钟。

为了循环这个迭代器，我们使用了 for-await-of 语句。

```
// generator-async-counter.js (continued)

const logIterator = async (iterator) => {
  for await (const item of iterator) {
    console.log('writing:', item);

};

logIterator(counterIterator); 
```

Enter fullscreen mode Exit fullscreen mode

结果正如所料。

```
$ node generator-async-counter.js
reading: 1
writing: 1
reading: 2
writing: 2
reading: 3
writing: 3
reading: 4
writing: 4
reading: 5
writing: 5
... 
```

Enter fullscreen mode Exit fullscreen mode

与基于推的流不同，异步生成器只在拉时生成一个新项。要确认这一点，您可以如下修改 logIterator。

```
// modify the function in generator-async-counter.js

const logIterator = async (iterator) => {
  for await (const item of iterator) {
    console.log('writing:', item);
    await new Promise(r => setTimeout(r, 5 * 1000));

}; 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在本文中，我们实现了四个无限计数器，并看到了流和生成器在这个例子中的行为是相似的，但却是根本不同的。流对数据源有更多的控制，而对生成器中的循环有更多的控制。我们还看到了行为差异，流有缓冲区，而生成器通常没有。还有许多其他的差异，我们没有包括在这篇文章中。想要了解更多信息的读者可能想要查看[文档](https://nodejs.org/dist/latest-v10.x/docs/api/stream.html)。

* * *

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/441375fdfee1c124c713d0800bb5e0d5.png)](https://logrocket.com/signup/)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

比较 Node.js v10 中的流 API 和(异步)生成器的帖子[首先出现在](https://blog.logrocket.com/comparing-the-stream-api-and-async-generators-in-node-js-v10/)[的 LogRocket 博客](https://blog.logrocket.com)上。
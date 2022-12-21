# JavaScript 中的多线程，算是吧

> 原文：<https://dev.to/aminnairi/multithreading-in-javascript-sort-of-1hbf>

我只是在开玩笑。JavaScript 是单线程的，我不会在这里使用 Web Workers。这是个诱饵。遗憾的...

## 但是等等，回来吧！

我将向您展示的是一个真正的游戏规则改变者，它可以帮助人们寻找减轻页面上脚本负载的解决方案。它甚至适用于那些不想/不能使用网络服务器的人。

## 源代码

```
<!DOCTYPE html>
<html>
  <body>
    <div id="app"></div>
    <script>
      "use strict";

      function loadScript(src) {
        return new Promise(function(resolve) {
          const script = document.createElement("script");

          script.setAttribute("async", true);
          script.setAttribute("src", src);
          script.addEventListener("load", resolve);

          document.head.appendChild(script);
        });
      }

      async function main() {
        await Promise.all([
          loadScript("https://unpkg.com/vue/dist/vue.js"),
          loadScript("https://unpkg.com/vue-router/dist/vue-router.js")
        ]);

        const { Vue, VueRouter } = window;

        console.log(Vue);        // ƒ Vue (options)
        console.log(VueRouter);  // ƒ VueRouter (options)
      }

      main();
    </script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

## 交代

### 该功能

我写的函数是异步函数。你可以从它的返回值看出它是一个承诺。如果你还不熟悉 promises，我强烈建议你阅读 Mozilla Developper 的 documentaton 网站上的使用 Promise 指南[。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)

这也意味着当在脚本中被调用时，它不会阻塞主线程。这对我们来说是理想的，因为我们现在是脚本的巨大消费者。

### 主要功能

这里的主函数是因为我们还没有顶级的 await。`async`和`await`关键字是在命令式风格中使用 promise 的语法糖。还是那句话，如果你不熟悉那些关键词，你可以在这里了解一下[。我也可以这样写:](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Async_await) 

```
Promise.all([loadScript("..."), loadScript("...")]).then(function() {
  //...
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 并行加载

你可能会奇怪，为什么我没有写一个简单的 for 循环呢？如果我想使用 for 循环，下面是我应该写的代码:

```
const urls = [
  "https://unpkg.com/vue/dist/vue.js",
  "https://unpkg.com/vue-router/dist/vue-router.js"
];

for (const url of urls) {
  await loadScript(url);
} 
```

Enter fullscreen mode Exit fullscreen mode

但在这种情况下，这和我写的原代码无关。这个循环将花费更长的时间，因为在开始加载第二个脚本之前，它必须等待第一个脚本加载。这不是很有效。`Promise.all`将同时并行加载它们。当然哪个更快。

## 结论

如果你必须在你的页面中使用大量的脚本，这是一个巧妙的小技巧，因为这可以加快页面的加载速度。您仍然容易出现网络延迟和我在这里没有提到的问题。我把它作为读者的一个练习。在加载有缺陷的脚本时，`Promise.all`调用尤其存在问题。

你怎么想呢?你将来会在你的网站上使用这个技巧吗？请在下面的评论区告诉我！
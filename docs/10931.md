# JavaScript Async/Await 优于简单承诺的 7 个理由(教程)

> 原文：<https://dev.to/gafi/7-reasons-to-always-use-async-await-over-plain-promises-tutorial-4ej9>

Async/await 是在 NodeJS 7.6 中引入的，目前所有现代浏览器都支持它。我相信这是自 2017 年以来 JS 最大的一项增加。如果你不相信，这里有一堆理由和例子说明为什么你应该立即采用它，并且不要回头。

### 异步/等待 101

对于那些以前从未听说过这个话题的人，这里有一个简单的介绍

*   Async/await 是一种编写异步代码的新方法。以前异步代码的替代方法是回调和承诺。
*   Async/await 实际上只是建立在承诺之上的语法糖。它不能用于普通回调或节点回调。
*   Async/await 和 promises 一样，是非阻塞的。
*   Async/await 使异步代码的外观和行为更像同步代码。这是它的全部力量所在。

### 语法

假设有一个函数`getJSON`返回一个承诺，这个承诺用某个 JSON 对象来解析。我们只想调用它并记录那个 JSON，然后返回`"done"`。

这就是你如何用承诺来实现它
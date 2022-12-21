# 异步和等待的基础

> 原文：<https://dev.to/pachicodes/basics-of-async-and-await-50m9>

你好，今天我将分享我关于 Async/Await 的笔记。

Async/await 函数是 ES2017 (ES8)附带的新功能，它允许我们编写看起来同步的代码来执行异步任务。使用 Async/Await 可以使你的代码更容易阅读和理解，并允许你在不阻塞主线程的情况下以同步的方式使用承诺。

**一点语法**
在一个函数使这个函数返回一个承诺之前指定单词 **async** 。

```
async function() {
} 
```

Enter fullscreen mode Exit fullscreen mode

**Await** 只在一个异步函数中工作，它在求解后返回 Promise 的结果。例如，Await 告诉 JS“等待”,直到承诺得到解决，然后再继续执行其余的代码。

```
const example = async function() {
const promise = new Promise(function(resolve, reject) {
setTimeout(resolve, 999, 1)
})
const response = await promise
console.log(response)

} 
```

Enter fullscreen mode Exit fullscreen mode

**处理错误**
在承诺被拒绝和错误被解雇之间有一点延迟，所以使用“尝试/捕捉”来处理错误是一个很好的策略，捕捉会在哪里，你猜怎么着？是的，它将捕捉 try 块中的任何错误。

这只是我一如既往的关于题目的简短笔记，所以一如既往的超级欢迎所有多余的评论！

周一快乐，感谢阅读，
XOXO
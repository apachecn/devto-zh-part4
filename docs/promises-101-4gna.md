# JavaScript 承诺 101

> 原文：<https://dev.to/niharikapujari/promises-101-4gna>

# JavaScript 中的承诺是什么？

定义:

承诺是一个可能在未来某个时间产生单一价值的对象:或者是一个已解决的价值，或者是一个未解决的原因。

## 现实生活中的例子

“想象你是一个孩子。你爸爸答应你他下周会给你买一个新玩具

那是一个**承诺**。承诺有三种状态:

1.  待定:你不知道你是否会得到玩具
2.  履行:爸爸很高兴，他会给你一个玩具
3.  拒绝:你爸爸不高兴，他扣留了玩具

## 什么是异步编程？

在编程中，我们可以把同步代码的定义简化为“一堆按顺序排列的语句”；因此，代码中的每条语句都是一条接一条地执行的。这意味着每条语句都必须等待前一条语句执行完毕。异步代码在主程序流之外执行语句，允许异步调用之后的代码立即执行，而无需等待。

## 许诺状态:

[![Promise-states](img/baebe579d66aefe72061e665b6e2d18c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8iwmci2O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jhzesqxefydl9yd1ntaf.png)

*许诺语法:*
[![Promise-syntax](img/dbdef375a88bd21e7ebb20dc8056ad02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GkGIunAD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/738msx21ev1mcexu0dod.png)

## 消费承诺:

让我们创建第一个承诺，看看如何使用它:

[![Promise-consume](img/c162440d2ba0364a5b5975cf37fa094b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zXXNkHg1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g1xcxbefcfunol46vvmw.png)

## 承诺不同步！

当 JavaScript 被执行时，同步代码有可能阻止进一步的执行，直到它完成正在做的事情。长时间运行的 JavaScript 函数会使 UI 或服务器无响应，直到函数返回。显然，这会导致糟糕的用户体验。

让我们回到现实生活中的例子:

[![Promise-async](img/6daac9cb4fa11c64c469bfee36b3ebd7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fSIVjpfy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ag0npa9yf5gpn5bnaxab.png)

您可能期望这是输出结果？

[![expected-output](img/9f297110d17051a823d2346273f7eccf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MPpx0q1X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s3piehbeet4wp45h38ej.png)

然而，实际的输出序列是:

[![actual-output](img/7f312c38794a5dc4a8dc475ced0140b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FEvfeDk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m24p7tnggo86hnelh0vu.png)

你，这个孩子，在等待新玩具的时候，不会停止玩你的旧玩具。你会吗？这就是我们所说的**异步**，代码将运行而不会阻塞或等待结果。任何需要等待承诺才能进行的事情，你把它放在`.then`

## 我们为什么要用承诺？

在承诺之前，我们有回电。回调是在另一个函数完成执行之后**要执行的函数——因此得名**回调**。**

[![callback](img/2b894d122decdc3883fb78071e035789.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--deHnmy5Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rdqpv0lnz4eywz5l2n0x.png)

上面的代码片段引用了一个**回调函数**,因为我们嵌套了代码四次。为了逃离回调地狱我们用**许诺**。

[![why-promise](img/7564baecfad3c099ebe4fa307f63b578.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OOZS1wSB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h19utg625ipeasvuzakb.png)

这是一个链接承诺的完美例子。这是在异步任务完成后告诉 JavaScript 下一步该做什么的正确方式。then 方法的结果是一个承诺。

## 言而无信。种族()

Promise.race 是一个 JavaScript 内置函数，它接受一个可迭代的 Promises(例如 Array)作为参数。一旦 iterable 中传递的一个承诺被解决或拒绝，这个函数**就会异步地**返回一个承诺。

[![Promise-race](img/c67bb08f6e7d62ad7dbe93d2222ff3b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WZAj1N5Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/72cqry6i1cha9d9340qi.png)

## . all()

Promise.all 是一个接受一组承诺作为输入的承诺，当所有承诺都得到解决或其中任何一个被拒绝时，该承诺得到解决。

例如，我们有五个承诺，都发出异步请求，比如从数据库获取数据，Promise.all 将根据承诺的执行返回一个 resolve 或 reject 状态。承诺本身就是一种承诺。

[![Promise-all](img/2f409b5629ad65d3233d3954ef0d9e63.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TMkho8Wv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e4jwo9jcjrufvabqxcfv.png)

## 错误处理:

在承诺中抛出的错误对象，因此它们不会告诉你任何关于导致错误的动作序列的信息；他们会告诉你的只是错误产生的文件和行号。所以我们需要使用更好的错误处理机制来记录这些错误。

[![Promise-error-handling](img/4e4237a2a8f3d860ac5a9e05a2c0257a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--01GPZFYF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uk691d3g3hx09ytt1god.png)

如果没有错误**。catch** 根本不触发。但是如果任何一个承诺被拒绝(一个网络问题或者无效的 json 或者其他什么)，那么它就会捕获它。

## 承诺.拒绝()

Promise.reject()返回被拒绝的承诺。它主要用于调试和错误捕获。

[![Promise-reject](img/d9b098e279381223622bbf58ccf0c0c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8eSYsnj_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xoik9hoom6j48l0wq5gz.png)

## 你做到了！

承诺已经成为 JavaScript 不可或缺的一部分。现在还不要担心街区里新来的小孩。希望这篇文章能帮助你掌握承诺的基本构件。
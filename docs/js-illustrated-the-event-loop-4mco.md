# JS 图解:事件循环🔁

> 原文：<https://dev.to/kapantzak/js-illustrated-the-event-loop-4mco>

Javascript 是单线程的，但是开发人员可以用 Javascript 编写异步代码！

但是怎么可能呢？事件循环使之成为可能！

在我们开始之前，让我们定义事件循环运行的环境。我们假设我们正在处理由浏览器执行的 Javascript 代码(不是在节点或其他环境中)。

让我们见见故事中的英雄

# 调用堆栈

[![Alt Text](img/3763bbc39b12516b1bdd398edb31dc1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NhY4bBRY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vt1lq8dr1xnia6p2u9cp.png)

调用堆栈是内存中的一个地方，它记录当时正在执行的函数，以及在那之后将要执行的函数。每个函数都放置在前一个函数之上。添加的第一个函数将最后执行(先入后出)。

# web API

[![Alt Text](img/6fd861a097b070e3fb25f7fb2c9d1fd3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sHHJ-wJP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vuqg7xfop4hy8bvw3hu1.png)

web API 不是核心 JS 的一部分，相反，它提供了 Javascript 程序可以使用的各种方法，比如`setTimeout()`或`alert()`。

# 消息队列

[![Alt Text](img/34cf546e518aa0e7db26e23053859efd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f_-fYGs5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t7p6bgzqu9q3h5j5gmsh.png)

消息队列是等待相关函数执行的消息列表。每次事件侦听器监视的事件发生时，都会向列表中添加一条新消息。

# 事件循环

[![Alt Text](img/6f94434e75d17ac0fc7d604ebe1ad613.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QVz9BcR1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jr8h8zb75ultqptp3r5o.png)

事件循环是一个持续运行并检查调用堆栈是否为空的进程。如果调用堆栈为空，它将消息队列的第一项推入调用堆栈以供执行。

这是浏览器环境

[![Alt Text](img/0ea1b0504bee71316fad63bb3d545e79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EKV_A_1Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uge9s4sxw8f2tf5nbjz6.png)

# 一个 JS 故事

让我们看看下面的代码，看看会发生什么
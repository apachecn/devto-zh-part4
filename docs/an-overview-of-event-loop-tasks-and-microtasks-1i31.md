# 事件循环、任务和微任务概述

> 原文：<https://dev.to/sagarrth/an-overview-of-event-loop-tasks-and-microtasks-1i31>

> *   The browser uses an event loop to effectively switch between scripts, rendering, painting, networking, events and similar other jobs.
> *   Although JS is single-threaded, with the help of event loops and a bunch of Web APIs, concurrency is possible.

## 什么是任务(宏任务)？

任务是要执行直到完成的代码。对于事件循环的每一轮，执行一个任务。一个任务可以调度其他任务(本质上是异步的)。浏览器维护多个任务队列。

任务源是 DOM 操作、UI 事件、历史遍历、网络

[https://stackblitz.com/edit/macrotasks?embed=1&&](https://stackblitz.com/edit/macrotasks?embed=1&&)

考虑到`setTimeout`的使用，它帮助我们推迟代码执行。每个`setTimeout`方法的定时器复位后，回调函数将被推送到任务队列进行处理。每个定时器都独立于主线程运行。这样它就不会阻塞主线程。

## 什么是微任务？

这是需要在当前执行的任务完成后执行的代码。

任务(宏、微任务)可以调度更多的任务，它们被添加到各自的队列中。微任务本质上是一种阻塞。与宏任务不同，主线程将被阻塞，直到微任务队列为空。所有这些都将在事件循环的同一轮中处理

微任务源有- Promise.resolve、Promise.reject、变异观测器、交叉观测器等。

[https://stackblitz.com/edit/microtasks?embed=1&&](https://stackblitz.com/edit/microtasks?embed=1&&)

通过将 for 循环中的迭代次数增加到一个较大的值，可以在上面的代码中展示微任务的阻塞特性。

微任务队列在下一个渲染和绘制作业之前被处理。如果它们长时间运行，将会导致视觉退化。

## 总结

事件循环算法的总结版本-

*   处理任务队列中最早的任务
*   如果有一个微任务队列，处理所有的条目直到它被清空
*   做渲染，绘画等等
*   如果任务队列不为空，重复上述步骤，否则等待

## 参考文献

*   [规格](https://html.spec.whatwg.org/multipage/webappapis.html#event-loops)
*   菲利普·罗伯特的精彩演讲
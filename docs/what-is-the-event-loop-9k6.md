# 什么是事件循环？

> 原文：<https://dev.to/taorep/what-is-the-event-loop-9k6>

*查看原帖@ jayess.lifesandwich.co*[T3T5】](https://jayess.lifesandwich.co/blog/2019/08/javascript-es6-const-let/)

很神秘。也许你从来没听说过。如果您是一名处理浏览器/ http 请求的开发人员，那么您很可能已经利用了它。它允许 setTimeout 工作，允许做出承诺，以及如何处理 async/await。

## **但是第一；JavaScript 引擎**

1)浏览器在网络上工作。

web 使用 JavaScript。

3)因此，浏览器有 javascript 引擎(来运行 JavaScript)

Chrome 的 JavaScript 引擎叫做 V8。构建了相同的 V8 Node.js。

V8 是一个单线程执行引擎。单线程意味着一个调用堆栈。执行引擎的意思是，它执行代码。

### **V8**

V8 有两个主要组件:内存堆和调用栈。

**内存堆**

这与细节无关，但是内存堆处理 JavaScript 的存储方式和位置。如果您创建了一个名为 helloWorld 的函数，然后想在以后使用它，内存堆就是存储它并记住它在哪里的地方，所以当您想使用它时，它就工作了。基本就是这样。

**调用堆栈**

调用堆栈是一种数据结构，允许从顶部添加或删除函数。

因为 JavaScript 和 V8 是单线程的，所以在 V8 中只有一个调用栈。每当我们在 JavaScript 中执行一个动作/函数时，那个东西就被推到调用堆栈上，在那里它将运行到完成，然后从顶部弹出(或移除)。

如果被调用的函数包含另一个函数调用，它将被推送到当前调用之上的调用堆栈上。这将继续下去，直到所有代码/调用都已执行，它们将从顶部一个接一个地被删除。

## **回到事件循环**

由于 JavaScript 一次只能做一件事，任何需要或者应该异步完成的事情都必须依赖浏览器本身提供的功能。大多数浏览器都有浏览器 API 来处理实际的请求，然后使用任务队列和事件循环来确定何时应该完成请求。

#### **一个简单的例子**

1)浏览器接收您编写的 Javascript 源代码并解释它

2)当浏览器执行代码时，如果它遇到诸如 http 请求之类的浏览器 API，它将把它推到堆栈上，开始一个新的、单独的检索响应的过程，并将调用弹出堆栈

3)浏览器将像以前一样继续执行代码

4)一旦分离过程完成(例如 http 请求)它被发送到任务队列。

5)响应将在队列中等待，直到事件循环确定调用堆栈为空*并将它推送到调用堆栈，在那里执行回调/承诺/等待的任何内容

6)执行后，它从堆栈中弹出，继续正常执行或根据需要重复这些步骤。

这是你要的部分

## **那么什么是事件循环呢？**

**事件循环**是一个检查调用堆栈的编程结构，当它为空时，会将队列中下一个等待的回调或承诺推送到调用堆栈。

就是这样。

这是一个小程序，它将来自异步请求(promises、setTimout、http 请求)的流量流引导回唯一的 JS 调用栈的主流。

## **星号**

不是一个，而是两个队列:

一个**微任务队列**等待承诺。

一个**任务队列**用于其他所有事情。

您还应该知道，事件循环**将微任务的优先级置于常规任务之上。**

这意味着在调用堆栈为空后，在任务队列中等待的正常任务响应将被推送到调用堆栈上。就像，没东西可烧了。

这里的是一个很酷的工具，向你展示这是如何工作的(抱歉，没有微任务队列)

然而，对于微任务来说，情况并非如此。微任务将在代码结束和更多代码开始之间触发。

例如:

如果您有三个 console.logs 和一个设置为 0 毫秒的 setTimeout,其中包含一个函数调用，那么您将从上到下获得正常的同步代码执行，然后在 console.logs 完成之后，setTimeout 函数调用将会运行。

但是对于像 promises 这样的微任务，假设你的承诺在 console.logs 上面的文件的顶部，它会尽快启动。在文件末尾，在第一个 console.log 之后，在第二个 console.log 之后，等等。它不在乎。一旦有东西弹出堆栈，并且正常的执行即将继续，**微任务队列中的所有微任务都会滑入调用堆栈中的位置，直到所有的微任务都完成，它们才会让代码继续运行。**

这与任务队列中一次运行一个任务(每个事件循环一个任务)的正常任务相反。

明白了吗？

酷毙了。那很容易。

* * *

*查看原帖@ jayess.lifesandwich.co*[T3T5】](https://jayess.lifesandwich.co/blog/2019/08/javascript-es6-const-let/)
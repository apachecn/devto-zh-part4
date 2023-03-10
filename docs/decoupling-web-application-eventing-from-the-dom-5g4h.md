# 从 DOM 中分离 Web 应用程序事件

> 原文：<https://dev.to/jerng/decoupling-web-application-eventing-from-the-dom-5g4h>

有时候我觉得，我写的东西毫无意义。然后我把它们框起来，以便进一步考虑。

## 

TL；大卫:你们能告诉我一些项目吗？

> 1.  Implement "event" and "event listener" ...
> 
> 2.  ... outside the DOM (events are not attached to DOM elements)

?

* * *

我刚刚发现了一个常见 web 应用程序的 bug。这个问题困扰了我一段时间，我终于可以放心了(我想)。购买者自负:这是一个在整个 web 应用程序生态系统中广泛存在的架构错误，因此它可能不容易被视为一个错误。

DOM 应该是一个文档，而不是业务处理器。但是几年来，人们一直将 DOM 事件&事件处理程序用作业务处理器，而不仅仅是文档。这极大地混淆了 DOM 树、Web 应用程序 API(Javascript)和呈现(CSS 等)之间的分离。).

碰巧的是，通常情况下，唯一放置事件监听器的地方，也是唯一触发事件的地方，是在 DOM 元素上。因此，在追求将事件驱动的业务流程写入代码的过程中，人们一直在将事件驱动的业务流程写入 DOM。

web 应用程序的数据结构不应该依赖于 DOM 树。因此{ web 应用程序中的数据模型}(2019 年理解为“商店”)和{ DOM 中的特定节点树}之间的紧密耦合是一种反模式。

但是您可能会问，DOM 不应该作为接收用户输入和向用户发送反馈等用户界面操作的媒介吗？是的，当然，这些可以被建模为事件，DOM 为我们提供了与这些事件相关的数据结构，以及操作这些结构的方法(监听器)。

但这并不意味着 DOM 也应该处理整个 web 应用程序中事件的流量控制。web 应用程序应该有一个位于 DOM 之外的事件控制中心。(也就是说，在任何灯光、阴影或虚拟 DOM 之外，以防不清楚)。我们通常知道如何在服务器上这样做，因为 NodeJS 在这方面引起了愤怒。但是 web 应用程序并不总是应该在服务器上运行——很多时候，我们希望 web 应用程序能够在客户端健壮地运行，并且只依赖服务器来实现联邦数据存储。那么，我们如何着手编写在 DOM 之外具有事件驱动流量控制的 web 应用程序客户机呢？

考虑到这一点，我在 DOM 之外搜索事件监听器的实现，并被这个[线程](https://stackoverflow.com/questions/15308371/custom-events-model-without-using-dom-events-in-javascript)指向了[反应器模式](https://subscription.packtpub.com/book/web_development/9781783287314/1/ch01lvl1sec09/the-reactor-pattern)。

我期待着更仔细地检查它，也许有你的投入。
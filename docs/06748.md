# Node.js 事件循环如何轮询

> 原文：<https://dev.to/alexhwoods/how-the-node-js-event-loop-polls-49lk>

假设我们是 Node.js 客户端，我们向某个服务器发出了请求。当我们等待回应时会发生什么？事件循环如何知道何时将相关的回调放到事件队列中？

假设我们是 Node.js 客户端，我们向某个服务器发出了请求。

*   当我们等待回应时会发生什么？
*   事件循环如何知道何时将相关的回调放到事件队列中？

## 解复用和事件循环

Node.js 的事件循环是在一个名为 [libuv](http://docs.libuv.org/en/v1.x/) 的库中实现的，这个库也被 Julia 和 Python 使用。我们将深入它的内部。

以下是事件循环的 1 次迭代:

```
uv__update_time(loop);
uv__run_timers(loop);
uv__run_pending(loop);
uv__run_idle(loop)
uv__run_prepare(loop);

// our method of interest
+------------------------------+
| uv__io_poll(loop, timeout);  |
+------------------------------+

uv__run_check(loop);
uv__run_closing_handles(loop); 
```

我们关心的方法`uv__io_poll`，基本上做了以下事情:

假设事件循环正在观察 *n* 个打开的套接字👀，因为我们有 *n* 个未解决的请求。它通过维护一个**观察器队列**来做到这一点，这个队列只是一个 *n* 观察器的列表——基本上是一个带有一些元数据的套接字。

然后，轮询机制接收一个事件。在通知时，它还不知道这对应于哪个打开的套接字。

我们所有的观察器(在观察器队列中)都由一个**文件描述符**标识。这只是一个整数，作为开放 I/O 资源的 ID。这是一个操作系统中常见的事情。

我们收到的事件包含一个 id 字段(名为`ident`)，这是一个文件描述符。一旦我们有了文件描述符，我们就可以得到监视器。这就是这个过程被命名为*解复用*的步骤。

最后，一旦我们有了观察器，我们就可以将回调放到事件队列中。

## 轮询机制？

在上面的描述中，我们忽略了一些看起来有点神奇的东西——什么是轮询机制以及事件循环如何接收事件？

简单的回答是，它使用一个系统调用来通知这样的事件。哪一个取决于操作系统。

| 操作系统（Operating System） | 功能 |
| --- | --- |
| FreeBSD (Mac) | [kqueue](https://en.wikipedia.org/wiki/Kqueue) |
| Linux 操作系统 | [epoll](https://en.wikipedia.org/wiki/Epoll) |
| Windows 操作系统 | [IOCP](https://docs.microsoft.com/en-us/windows/win32/fileio/i-o-completion-ports) |

让我们看一看`kqueue`，但是首先让我们回顾一下当我们的计算机接收到一个数据包时会发生什么。

> 当内核从网络接口获得数据包时，它会对数据包进行解码，并根据源 IP、源端口、目的 IP 和目的端口判断出数据包与什么 TCP 连接相关联。该信息用于在内存中查找与该连接相关联的结构 sock。假设数据包是有序的，那么数据有效载荷就被复制到套接字的接收缓冲区中。[【3】](#sources)

```
How kqueue recieves a notification:

                           +--------------------------+
                           |                          |          +-------------+         +-------------+   
                           |                          |          |             |         |             |   
receives packet +--------->+    Network Interface     +--------->+   Socket    +-------->+   kqueue    |   
                           |                          |          |             |         |             |   
                           |                          |          +-------------+         +-------------+   
                           +--------------------------+ 
```

在这之后，套接字(我们感兴趣的事件生成实体)遍历 kqueue 的注册事件列表(称为`knotes`)，并找到它所属的事件。过滤功能决定它是否值得报告。`kqueue`然后将它报告给用户程序。

下面是应用程序可能向`kqueue`注册的一些事件。

| 事件名称 | 跟踪操作 |
| --- | --- |
| EVFILT_READ | 描述符有要读取的数据 |
| 亵渎者 | 与描述符关联的异步 I/O 已完成 |
| EVFILT_TIMER | 基于事件的计时器已过期 |

实际上非常简单。它只是一个 FreeBSD 系统调用，向用户程序提供内核事件的通知。

在我们的例子中，libuv 是用户程序。

## 结论

这无疑帮助我理解了 libuv 的核心。它为节点提供事件循环；它使用回调风格的 API，最重要的是，它抽象了与系统调用接口的复杂性。

它的“轮询”机制本质上并不复杂，因为它使用的系统调用是事件驱动的。它只需要保存为每个事件注册的回调的数据结构。

## 来源

1.  [Libuv 源代码](https://github.com/libuv/libuv)
2.  [FreeBSD 操作系统的设计与实现](https://www.amazon.com/Design-Implementation-FreeBSD-Operating-System/dp/0201702452)
3.  [TCP 套接字如何工作](https://eklitzke.org/how-tcp-sockets-work)
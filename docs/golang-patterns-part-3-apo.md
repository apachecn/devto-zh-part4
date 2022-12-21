# Golang 模式-第 3 部分

> 原文：<https://dev.to/napicella/golang-patterns-part-3-apo>

在这篇文章中，我将展示一个使用 golang 通道的很酷的模式:**通道的通道**。

在 golang 中，频道是一个非常灵活的工具。学会正确使用它们，释放围棋的全部力量。但是这是**而不是**一篇解释什么是频道或者你什么时候需要使用它们的文章。
如果你想了解更多关于渠道的信息，我建议你阅读:

*   [https://blog.golang.org/share-memory-by-communicating](https://blog.golang.org/share-memory-by-communicating)
*   [https://golang.org/doc/effective_go.html#channels](https://golang.org/doc/effective_go.html#channels)
*   [https://medium . com/rungo/anatomy-of-channels-in-go-concurrency-in-go-1ec 336086 ADB](https://medium.com/rungo/anatomy-of-channels-in-go-concurrency-in-go-1ec336086adb)
*   [https://dev.to/search?q=go%20channels](https://dev.to/search?q=go%20channels)

### 一个玩具问题

假设您需要定期运行某项任务，例如每秒运行一次。
但是时不时需要尽快运行，意思是不能等任务触发。
一个典型的例子是文件同步工具，它可以在不同的主机之间同步文件。
你想每 **x** 秒同步一次文件，但偶尔又想按需同步；基本上是在预定义的时间表之外强制同步。

### 渠道中的渠道

这是着手解决问题的一种方式，一种利用渠道的渠道的方式。

```
const (
    running    int32 = 1
    notRunning int32 = 0
)

type Worker struct {
    syncStrategy      func() error
    period            time.Duration
    currentStatus     *int32
    periodicSyncChan  chan int
    outOfBandSyncChan chan chan error
    exitChan          chan int
}

func NewSyncWorker(syncStrategy func() error, period time.Duration) *Worker {
    periodicSyncChan := make(chan int)
    outOfBandSyncChan := make(chan chan error)
    exitChan := make(chan int)
    var currentStatus = notRunning

    return &Worker{
        syncStrategy:      syncStrategy,
        period:            period,
        currentStatus:     &currentStatus,
        periodicSyncChan:  periodicSyncChan,
        outOfBandSyncChan: outOfBandSyncChan,
        exitChan:          exitChan,
    }
}

func (s *Worker) Sync() error {
    if atomic.CompareAndSwapInt32(s.currentStatus, notRunning, running) {
        return s.doSync()
    } else {
        return errors.New("Sync is already running")
    }
}

func (s *Worker) doSync() error {
    if e := s.syncStrategy(); e != nil {
        return e
    }
    time.AfterFunc(s.period, func() {
        s.periodicSyncChan <- 1
    })

    for true {
        select {
        case <-s.periodicSyncChan:
            {
                s.syncStrategy()
                time.AfterFunc(s.period, func() {
                    s.periodicSyncChan <- 1
                })
            }
        case responseChannel := <-s.outOfBandSyncChan:
            responseChannel <- s.syncStrategy()
        case <-s.exitChan:
            {
                atomic.CompareAndSwapInt32(s.currentStatus, running, notRunning)
                return nil
            }
        }
    }
    return nil
}

func (s *Worker) OutOfBandSync() error {
    responseChannel := make(chan error)
    s.outOfBandSyncChan <- responseChannel
    return <-responseChannel
}

func (s *Worker) Stop() {
    s.exitChan <- 1
}

func (s *Worker) IsRunning() bool {
    return *s.currentStatus == running
} 
```

Enter fullscreen mode Exit fullscreen mode

**out of bandsynchan**是通道中的通道。
当客户端请求一个 **OutOfBandSync** 时，该函数创建一个 **responseChannel** 。
将 **responseChannel** 推入**outofbandsynchan**中，当操作完成时，它将用于返回值(在本例中为空或错误)。

换句话说，我们是:

*   创建一个通道将响应发送回客户端
*   通过另一个通道将新创建的通道转移到负责同步的代码

更重要的是，我们没有使用锁。不同的围棋程序通过以一种非常优雅的方式互相发送消息来进行通信。

### 多戈朗图案

如果你喜欢这篇文章，请查看:

*   [第一部分](https://dev.to/napicellatwit/golang-patterns-5a64)
*   [第二部分](https://dev.to/napicellatwit/golang-patterns---part-2-1906)

你可以在 [twitter](https://twitter.com/napicellatwit) 和 [github](https://github.com/napicella) 上找到我

玩得开心！
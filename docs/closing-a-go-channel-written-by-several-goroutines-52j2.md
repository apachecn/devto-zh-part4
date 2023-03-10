# 关闭由几个 goroutines 编写的 Go 通道

> 原文：<https://dev.to/leolara/closing-a-go-channel-written-by-several-goroutines-52j2>

在这篇文章中，我展示了一个关闭由几个 goroutines 编写的 Go 通道问题的解决方案。我们看到了解决方案的过程和推理，在这个过程中，我们使用了几个标准的 Go 并发工具，如关闭同步通道、原子类型、选择和等待组。

## 简介

我在构建开源项目传送带([https://github.com/leolara/conveyor](https://github.com/leolara/conveyor)、[https://github.com/leolara/conveyor-impl](https://github.com/leolara/conveyor-impl))的时候，发现了一个 Go 开发的有趣问题。如何关闭由几个 goroutines 编写的频道？更确切地说，在我的情况下，你控制了作者而不是读者。

在研究这个问题时，我发现了这篇文章[https://go101.org/article/channel-closing.html](https://go101.org/article/channel-closing.html)，但它实际上说你不应该尝试:

> 如果一个通道有多个并发发件人，请不要关闭该通道。

引用的文章提出了几个解决方法，希望你在某个特定的问题上给读者写信。我想在读者可以像使用`for range`那样以惯用方式阅读时关闭通道，这样当通道关闭时，他们就正常退出循环。

在这篇文章中，我不仅想提供一个具有良好代码质量且健壮的解决方案，还想展示实现它的过程和推理。这样的话，我认为对刚开始并行编程的人会有帮助。

## 问题的核心

主要的困难来自于这样一个事实:在一个关闭的通道上写东西总是会死机，即使你是在它关闭之前写的。你没看错，当你在一个通道上写的时候，goroutine 通常会阻塞，如果在数据被读取之前通道被关闭，被阻塞的 goroutine 会死机。

当你从一个频道中读取时，这种情况不会发生，在 Go 中你可以读取它，并找出它是否被关闭(在读取之前或被阻止读取时)。不幸的是，当写入通道时，Golang 没有实现同样的行为。我不知道原因，一个可能的猜测是，这个设计决定可能会使围棋频道更有效。

## 一个首发的例子

```
package gochannels

type Publisher struct {
    ch chan string
}

func (p *Publisher) Read() <-chan string {
    return p.ch
}

func (p *Publisher) write(data string) {
    go func() {
        p.ch <- data
    }()
}

func (p *Publisher) Close() {
    close(p.ch)
}

// there are other Publisher methods 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，`Publisher`中的其他内部代码将调用私有方法`write`向一个或多个阅读器发送数据，例如作为对网络消息的反应。当然，如果在通道关闭后调用`write`,通道中的实际写操作将会死机。这很容易解决，例如使用原子标志:

```
 type Publisher struct {
    ch chan string
    closing uint32
}

func (p *Publisher) write(data string) {
    if atomic.LoadUint32(&p.closing) != 0 {
        return nil
    }
    go func(data string) {
        p.ch <- data
    }(data)
}

func (p *Publisher) Close() {
    atomic.StoreUint32(&p.closing, 1)

    close(p.ch)
} 
```

Enter fullscreen mode Exit fullscreen mode

但这并没有解决我们的主要问题，如上所述，即如果在通道被阻止写入通道之后，通道读取器解除通道之前关闭通道，`write`中的 goroutines 将会死机。

## 一些实验

第一个直接的解决方法是在通道关闭时让写入死机，并使用 recover 捕获死机。如果你不想过多考虑这个问题，这可能是一个很好的解决方案。这样你就失去了对正在发生的事情的控制，而且你还必须考虑其他类型的恐慌。然而，我确信在 Go 中有一个使用主要并发工具的解决方案，并且得到它至少是一个非常有趣的练习。

我想到的第一个想法是从通道中读取，直到所有的编写器都被打开，因此可以避免恐慌。使用`for range`循环:
很容易从一个通道中读取所有内容

```
 func (p *Publisher) Close() {
    atomic.StoreUint32(&p.closing, 1)

    for range p.ch {
    }

    close(p.ch)
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码首先设置一个标志(自动避免竞争情况),防止新的 goroutines 进入阻塞状态。之后，它进入一个循环，从通道中读取数据并丢弃数据，从而解锁所有在通道上写入的 goroutines。最后，它关闭了通道。

这显然是行不通的，因为代码会卡在循环中，只有当通道关闭时它才会退出循环，这是我们在读取了所有正在编写的 goroutines 并阻塞它们之后想要做的事情。按照这种推理方式，我们可以在一个 goroutine 中启动 reading 循环，然后等待它从所有被阻塞的 writers 中读取数据。

```
 func (p *Publisher) Close() {
    atomic.StoreUint32(&p.closing, 1)

    go func() {
        for range p.ch {
        }
    }()

    <-time.After(1 * time.Second)

    close(p.ch)
} 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，最大的问题之一是它能否工作。它在大多数情况下都是有效的，直到也许两年后它会开始让你的服务器持续崩溃，更重要的是*你永远不会想到今天让你的服务器崩溃的代码是两年前引入的*。

这就是为什么我觉得并发编程如此有趣，*bug 可以很好的隐藏起来*，你需要严谨，考虑周全。事实是，当一秒钟后仍有正在编写的子程序被阻塞时，我们的代码将会死机。这可能是由于请求过载造成的，或者仅仅是因为添加了其他 goroutines，从而降低了线程的可用性并因此被调度，或者仅仅是因为我们的虚拟机正在被迁移。

它会产生一个 bug，这个 bug 可能会被与导致 bug 的实际代码相距甚远的东西触发，因此很难被检测到。增加时间会使它更不可能，但也会使你的代码更慢。在知道等待时间只会降低问题发生的可能性的情况下，您会在多大程度上增加等待时间？

## 另试同`select`

看起来我们的主要问题是写者被阻塞了，唯一的解封方法是从通道中读取。但是我们还有另外一个工具。这将允许我们将通道上的写入与其他内容放在一起，如果`select`在通道关闭之前采用其他分支，那么我们就可以一起避免这个问题。

我们看到的另一个工具，也出现在介绍中提到的 go101 文章中，是作为信号关闭的通道。由于从一个关闭的通道中读取不会引起恐慌，您可以关闭一个通道，只作为该通道的读者的一个信号。

```
 type Publisher struct {
    ch chan string
    closingCh chan interface{}
}

func (p *Publisher) write(data string) {
    select {
    case <-p.closingCh:
        return
    default:
    }

    go func(data string) {
        select {
            case <-p.closingCh:
            case p.ch <- data:
        }
    }(data)
}

func (p *Publisher) Close() {
    close(p.closingCh)

    go func() {
        for range p.ch {
        }
    }()

    <-time.After(1 * time.Second)

    close(p.ch)
} 
```

Enter fullscreen mode Exit fullscreen mode

这看起来更健壮，它使用了通道和`select`，看起来好像所有被阻止的写入程序都将在`closingCh`时退出。

不过有一个重要的警告。在某个时刻，`closingCh`和`ch`都将关闭。人们很自然地会认为，如果其中一个分支可以继续进行，而另一个分支引起了恐慌，那么就应该选择好的分支。实际上它可以取任何一根树枝。

阅读文档[https://golang.org/ref/spec#Select_statements](https://golang.org/ref/spec#Select_statements)

> 如果一个或多个通信可以进行，则通过统一的伪随机选择来选择可以进行的单个通信。

实际上造成混乱的不是阻塞，因此可以选择那个分支，造成代码混乱。

我们又遇到了一个可能潜伏多年的问题，直到它变得致命。

## 解决方案涉及等待组

回到前面的代码，我们从通道读取以解锁写入器，我们的问题是我们不能明确知道所有写入器何时被解锁，所以我们认为唯一的解决方案是等待一段时间。解决方案是使用等待组。

我没有马上考虑等待组的原因是，在围棋中，它们通常出现在非常特殊的场景中。你启动一堆 goroutines，然后等待它们全部完成。等待操作通常在所有添加操作执行完毕后立即执行。

然而，等待组应该是比这更通用工具。等待组实际上应该是一个线程安全的计数器，等待操作会一直阻塞到计数器的值为零。我们可以递增和递减等待组计数器，可能达到几次零，然后再次增加，然后当我们需要它时，使用 wait 来阻塞，直到计数器达到零。

从这个角度来看，我们可以使用它作为一种方法来有序地拆除一个子系统，这个子系统产生了许多短命的 goroutines。所有 goroutines 在开始和结束时都会递增和递减同一个等待组。当我们想要停止子系统时，我们首先需要一种机制来防止系统启动新的 goroutines。从这一点，我们知道等待组只会减少，然后我们只需要等待等待组达到零。

正如我们将在下面看到的，其中一些假设是不正确的。

无论如何，让我们详细看看这个解决方案，回到我们之前的尝试，尝试从通道读取，直到所有的写入器都被解除阻塞:

```
package gochannels

type Publisher struct {
    ch chan string
    closing uint32
    writersWG sync.WaitGroup
}

func (p *Publisher) Read() <-chan string {
    return p.ch
}

func (p *Publisher) write(data string) {
    p.writersWG.Add(1)
    go func(data string) {
        defer p.writersWG.Done()
        if atomic.LoadUint32(&p.closing) != 0 {
            return nil
        }

        p.ch <- data
    }(data)
}

func (p *Publisher) Close() {
    atomic.StoreUint32(&p.closing, 1)

    go func() {
        for range p.ch {
        }
    }()

    p.writersWG.Wait()

    close(p.ch)
}

// there are other Publisher methods 
```

Enter fullscreen mode Exit fullscreen mode

这似乎解决了这个问题。

使用等待组还有其他选择。

有了这个解决方案，一个新事物出现了。使用这种等待组方法，并不真的需要我们自己来消耗作者。使用这种方法，我们可以让读取器读取所有挂起的写操作，然后关闭通道。

```
func (p *Publisher) CloseWithoutDraining() {
    atomic.StoreUint32(&p.closing, 1)

    p.writersWG.Wait()

    close(p.ch)
} 
```

Enter fullscreen mode Exit fullscreen mode

出现的另一个问题是，现在，起诉频道关闭的方法可能也有效。

```
 type Publisher struct {
    ch chan string
    closingCh chan interface{}
    writersWG sync.WaitGroup
}

func (p *Publisher) write(data string) {
    p.writersWG.Add(1)

    go func(data string) {
        defer p.writersWG.Done()

        select {
        case <-p.closingCh:
            return
        default:
        }

        select {
            case <-p.closingCh:
            case p.ch <- data:
        }
    }(data)
}

func (p *Publisher) Close() {
    close(p.closingCh)

    go func() {
        for range p.ch {
        }
    }()

    p.writersWG.Wait()

    close(p.ch)
}

func (p *Publisher) CloseWithoutDraining() {
    close(p.closingCh)

    p.writersWG.Wait()

    close(p.ch)
}

// there are other Publisher methods 
```

Enter fullscreen mode Exit fullscreen mode

在最后一个实现中需要注意的是，方法`write`中的第一个`select`是不阻塞的，因为它包含了一个`default`案例。

## 测试竞态条件

来自维基百科[https://en.wikipedia.org/wiki/Race_condition](https://en.wikipedia.org/wiki/Race_condition)

> 竞争条件或竞争危险是电子、软件或其他系统的条件，其中系统的实质性行为取决于其他不可控事件的顺序或时间。当一个或多个可能的行为不受欢迎时，它就成了一个 bug。

测试和发现竞争条件错误是非常复杂的。主要因素是，竞争条件的发生需要同一程序中指令之间的正确定时，因此它依赖于计算机当前的负载、操作系统如何进行上下文切换或实际的 CPU 版本和型号等因素。

触发竞争的一个重要因素是环境变量`GOMAXPROCS`,它决定了用于运行 Go 程序的操作系统线程的数量。值为 1 将隐藏许多可能的竞争条件，这可能是您系统中的默认值。此外，改变它的值，我们可以改变程序的时间，然后触发一个竞争条件的错误。go test 选项`-cpu`允许我们用几个不同的`GOMAXPROCS`值来运行测试，这将帮助我们发现竞争条件错误。

另一个非常有用的工具是标志`-race`。当执行 go 测试时，如果它检测到竞争情况，它会显示一条消息。

我所做的是用一列`-cpu`值运行 go 测试，激活标志`-race`，用`-count=1000`运行多次并覆盖不同的计时，一旦我们得到一个错误，我们也用`-failfast`停止测试。类似于:

```
go test -cpu=1,9,55,99 -race -count=1000 -failfast 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，运行几次会触发集成的竞争条件检测器。错误的重要部分是这个:

```
==================
WARNING: DATA RACE
Read at 0x00c000590078 by goroutine 138:
  sync.(*WaitGroup).Add()
      /usr/local/Cellar/go/1.12.6/libexec/src/internal/race/race.go:37 +0x169
  [...]/gochannels/solution.(*Publisher).write()
      /Users/leo/src/hugo/code/gochannels/solution/publisher.go:34 +0x4b
  [...]/gochannels/solution.(*Publisher).Run()
      /Users/leo/src/hugo/code/gochannels/solution/publisher.go:25 +0xb7
  [...]/gochannels/solution.TestIntermediate.func2()
      /Users/leo/src/hugo/code/gochannels/solution/publisher_test.go:75 +0x42

Previous write at 0x00c000590078 by goroutine 142:
  sync.(*WaitGroup).Wait()
      /usr/local/Cellar/go/1.12.6/libexec/src/internal/race/race.go:41 +0xef
  [...]/gochannels/solution.(*Publisher).Close()
      /Users/leo/src/hugo/code/gochannels/solution/publisher.go:61 +0x88
  [...]/gochannels/solution.TestIntermediate()
      /Users/leo/src/hugo/code/gochannels/solution/publisher_test.go:89 +0x2bd
  testing.tRunner()
      /usr/local/Cellar/go/1.12.6/libexec/src/testing/testing.go:865 +0x163 
```

Enter fullscreen mode Exit fullscreen mode

竞争检测器，警告我们当同时调用`Wait`和`Add`时发生的竞争情况。奇怪的是，来自用于同步 goroutines 的包的两个方法之间存在竞争。但是这个组件有一个警告，来自 https://golang.org/pkg/sync/#WaitGroup.Add[的文档](https://golang.org/pkg/sync/#WaitGroup.Add)

> 请注意，当计数器为零时发生的增量为正的调用必须在等待之前发生。计数器大于零时开始的负增量呼叫或正增量呼叫可能随时发生。通常，这意味着对 Add 的调用应该在创建 goroutine 或其他要等待的事件的语句之前执行。如果 waitGroup 被重用来等待几个独立的事件集，则新的 Add 调用必须在所有先前的 Wait 调用返回之后发生。请参见 WaitGroup 示例。

因此，如果同时调用`Wait`和`Add`以及一个正值参数`Add`，可能会有一些竞争条件错误，我猜标准库的开发者可能不得不在性能和/或复杂性上做出妥协。对`Done`的调用可以并发进行，没有问题。

有几个人在网上谈论这个问题，例如 https://groups.google.com/forum/#!topic/golang-nuts/Qq _ h0 _ M51YM 和[https://stack overflow . com/questions/53769216/is-it-safe-to-add-a-wait group-from-multiple-go routines](https://stackoverflow.com/questions/53769216/is-it-safe-to-add-to-a-waitgroup-from-multiple-goroutines)

在尝试了几种方法后，我找到了一个解决方案，用一个`sync.Mutex`锁包围所有对`Wait`和`Add(1)`的调用。

# 在 waitgroup 周围使用互斥锁

```
 type Publisher struct {
    ch chan big.Int

    closingCh      chan interface{}
    writersWG      sync.WaitGroup
    writersWGMutex sync.Mutex
}

func (p *Publisher) write(data string) {
    go func(data string) {
        p.writersWGMutex.Lock()
        p.writersWG.Add(1)
        p.writersWGMutex.Unlock()

        defer p.writersWG.Done()

        select {
        case <-p.closingCh:
            return
        default:
        }

        select {
            case <-p.closingCh:
            case p.ch <- data:
        }
    }(data)
}

func (p *Publisher) Close() {
    close(p.closingCh)

    go func() {
        for range p.ch {
        }
    }()

    p.writersWGMutex.Lock()
    p.writersWG.Wait()
    p.writersWGMutex.Unlock()

    close(p.ch)
} 
```

Enter fullscreen mode Exit fullscreen mode

起初，调用互斥锁中阻塞(`Wait`)的东西似乎不是个好主意，它似乎会无限期地持有互斥锁，并阻止其他人进入它。

看待互斥的一种方式是，它在锁定和解锁之间创建了一个“临界区”。这个临界区不能由一个以上的 goroutine 同时进入，或者说，所有的“临界区”执行都是一个接一个地进行，从不并行。

这样，我们可以在下面的事情中推理我们的问题:有两种类型的`Add(1)`，一些在`Wait`之前执行，一些在`Add(1)`之后执行。互斥锁防止了任何其他的可能性。

对于在`Wait`之前执行的`Add(1)`,我们可以看到，要么它们在读取器已经解除对其通道写操作的阻止时调用`Done`,要么因为在调用`Wait`之前，`closingCh`被关闭。因此，对于在`Wait`之前调用的所有`Add(1)`，无论如何`Done`都将被调用，从而将计数器减为零，并因此在某个点解锁`Wait`。这里重要的是，互斥锁不会阻止调用`Done`，因此在某个时候会解锁`Wait`。

我们已经看到,`Wait`总是会解除封锁。

现在，我们应该处理在`Wait`之后执行的所有`Add(1)`，我们可以看到，在这种情况下，`closingCh`将在调用`Add(1)`之前关闭，因此当它到达第一个`select`时，它将退出并调用`Done`，而不尝试在`ch`上写入，这将引起恐慌。

从这个推理中我们可以看到，这不会导致死锁，并且测试也证实了这一点。

这可能会对性能产生一些影响，但似乎并不重要。对`Add`的所有调用都将被排序，但这是一个快速且恒定的时间操作，因此不会产生太多争用。无论如何，这将是一个好主意。

由此而来的另一个有趣的研究是关于为什么标准等待组有这种竞争条件，以及它是否可以在不影响性能的情况下简单地修复。

## 解

这里有一个测试解决方案的完整示例:[https://gist . github . com/leolara/f 6 FB 5 DFC 04d 64947487 f 16764 d6b 37 b 6](https://gist.github.com/leolara/f6fb5dfc04d64947487f16764d6b37b6)

您可以尝试注释掉互斥体，并看到测试由于竞争条件而返回错误。

我们已经看到了关于并发性的推理是多么复杂。这不是一个复杂的场景，在更真实的场景中，并发可能是计算机科学中最具挑战性的事情之一。

感谢[https://github.com/TopherGopher](https://github.com/TopherGopher)的点评。

我希望你们都喜欢阅读这篇文章，不要犹豫发表意见。

干杯，利奥

最初发布于[https://www . leolara . me/blog/closing _ a _ go _ channel _ written _ by _ number _ go routines/](https://www.leolara.me/blog/closing_a_go_channel_written_by_several_goroutines/)
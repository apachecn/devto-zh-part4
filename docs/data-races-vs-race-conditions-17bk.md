# 数据竞争与竞争条件

> 原文：<https://dev.to/cronokirby/data-races-vs-race-conditions-17bk>

这是一篇关于`Data Races`和`Race Conditions`的区别，以及提供数据竞争自由的数据结构或模式为什么不能提供竞争条件自由的快速文章。

示例将在`Go`中给出，因为这是一种有一些
并发结构在这里发挥作用的语言，也是最初引发这篇博客文章的语言。

## 数据竞赛

我几乎完全同意
[rust 对数据竞赛](https://doc.rust-lang.org/nomicon/races.html)的定义。根据这个定义，数据竞争是指一个或多个线程同时访问内存/变量中的一个位置，其中至少有一个是写操作，并且至少有一个与其他线程不同步。

例如，对一个非同步变量的多个并发读取是完美的
好:

```
const a = 3

func main() {
    go func() {
        fmt.Printf("Thread B: %d\n", a)
    }
    fmt.Printf("Thread A: %d\n", a)
} 
```

尽管打印的顺序会随着执行的不同而不同，但是不会出现数据竞争，因为两个线程都只是从数据中读取数据。

如果我们现在有一个线程可变地访问`a`，我们引入了一个数据竞争:

```
func main() {
    a := 3
    go func() {
        a = 10
    }
    fmt.Printf("Thread A: %d\n", a)
} 
```

我们可以通过引入互斥锁来同步对`a` :
的访问来解决这个问题

```
func main() {
    a := 3
    var m sync.Mutex
    go func() {
        m.Lock()
        a = 10
        m.Unlock()
    }
    m.Lock()
    fmt.Printf("Thread A: %d\n", a)
    m.Unlock()
} 
```

两个线程同时访问`a`，其中一个正在写，但是由于访问是同步的，这就不再是数据竞争了。

## 竞赛条件

竞争条件源于并发程序中的`non-determinism`。理论上，任何来自并发的可观察到的非确定性都可以被视为竞争条件，但是实际上，什么构成了竞争条件取决于我们希望我们的程序尊重什么属性。

让我们以下面的程序为例:

```
func main() {
    go func() {
        for {
            fmt.Println("Thread B")
        }
    }
    for {
        fmt.Println("Thread A")
    }
} 
```

我们将看到两条消息的某种随机交错:

```
Thread A
Thread A
Thread B
Thread A
Thread B
Thread B
... 
```

如果打印的确切顺序是我们希望我们的程序尊重的属性，那么这可以被认为是一个竞争条件。我们可以使用某种形式的同步来加强打印的顺序。

实际上，即使执行不是确定性的，我们也不会认为这是一个竞争条件，因为这不是我们关心的属性。

简而言之，竞态条件是由于程序的并发执行而产生的对程序应该具有的属性的某种违反。

## 没有数据竞争的竞争条件

我决定发这个帖子的原因是我最近的一次讨论。有人声称使用 Go 的通道可以防止*竞争条件*,因为操作总是线程安全的。

的确，只要内存没有以其他方式共享，Go 的通道就不会出现数据竞争。也就是说，尽管只使用通道，编写一个具有竞争条件的程序还是很容易的。

在这个例子中，我们将有一个简单的服务器响应请求来获取整数值，并设置整数值。

消息看起来像这样:

```
type msg struct {
    id int
    amount int
} 
```

我们将使用`0`作为`get`的 id，使用`1`作为`set`的 id。

我们的服务器类型将如下所示:

```
type server struct {
  msgs chan msg
  resps chan int
}

func newServer() *server {
  msgs := make(chan msg)
  resps := make(chan int)
  return &server{msgs, resps}
} 
```

我们有一个能够为服务器发送和接收消息的通道，以及一个对这些消息进行响应的通道。

我们的服务器将在后台启动，功能如下:

```
func (s *server) start() {
  state := 0
  for {
    m := <-s.msgs;
    if m.id == 0 {
      s.resps <- state
    } else {
      state = m.amount
    }
  }
} 
```

我们通过发回当前状态来响应 get 请求，通过设置并发状态来响应 set 请求。因为只有一个线程控制状态，所以与服务器的交互不会出现数据竞争。

我们的服务器的基本操作如下:

```
func (s *server) get() int {
  s.msgs <- msg{0, 0}
  return <-s.resps
}

func (s *server) set(amount int) {
  s.msgs <- msg{1, amount}
} 
```

现在有了这些基本操作，我们可以定义下面的函数:

```
func (s *server) increment() {
  x := s.get()
  s.set(x + 1)
} 
```

这个函数只是增加状态。

在我们的主函数中，我们可以做以下事情:

```
func main() {
  s := newServer()
  go s.start()
  for i := 0; i < 200; i++ {
      s.increment()
  }
  fmt.Println(s.get())
} 
```

这将执行 200 次递增操作，如预期的那样，状态保持在 200。

但是如果我们开始在线程之间共享这些操作，我们会注意到一个竞争条件:

```
func main() {
  s := newServer()
  go s.start()
  go func() {
      for i := 0; i < 100; i++ {
          s.increment()
      }
  }
  for i := 0; i < 100; i++ {
      s.increment()
  }
  fmt.Println(s.get())
} 
```

像以前一样，我们预计会看到 200 个，但实际上我们会看到一个更小的数字。这是一个竞争条件。发生这种情况是因为 2 个线程可以在设置下一个值之前获得相同的值，然后两个线程都将设置相同的值，导致对 increment 的 2 次调用只执行一次增量。

## 简单的修复是不够的

我们想要的是一个原子操作。在这种情况下是原子增量。
原子增量意味着每个增量都作为一个单独的步骤发生，从而防止两个并发的增量只导致一个单独的操作。

我们可以给我们的服务器添加一个额外的消息，比如 id 为`2` :
的

```
else if m.id == 2 {
    state++
} 
```

问题是下面的函数仍然不是原子的:

```
func (s *server) doubleIncrement() {
    s.increment()
    s.increment()
} 
```

无论我们的服务器为其状态提供什么样的原子操作集，我们都不能简单地以不同步的方式依次执行多个操作，并期望结果也是原子的。

这个例子的完整代码可以在这里找到:
[https://play.golang.org/p/995MLEiqIVV](https://play.golang.org/p/995MLEiqIVV)

## 总结

数据竞争不应该与竞争条件混为一谈。仅仅因为一个数据结构提供了无数据竞争的操作，并不意味着该数据结构不会出现竞争情况。此外，对原子操作进行排序不会产生原子操作。

设计没有错误的并发程序并不简单，一旦你开始在多台计算机上工作，就会变得更加复杂。
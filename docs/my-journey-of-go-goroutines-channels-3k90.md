# 我的围棋之旅(Goroutines，Channels)

> 原文：<https://dev.to/k_penguin_sato/my-journey-of-go-goroutines-channels-3k90>

# 什么是 Goroutines？

一个`goroutine`是由 Go 运行时管理的轻量级线程。

# 如何启动 Goroutine

您可以通过在函数或方法调用之前添加关键字`go`来声明一个`goroutine`，如下面的代码所示。

```
go function_name(arg, ...) 
```

Enter fullscreen mode Exit fullscreen mode

在下面的代码中，`go hello()`启动一个新的`goroutine`，`hello()`将与`main`函数同时运行，该函数运行在它自己的 Goroutine 和它的`main Goroutine`T5 中

```
package main

import "fmt"

func hello() {
  fmt.Println("Hello World")
}

func main() {
  go hello()
  fmt.Println("main function")
  //=> main function
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码只输出`main function`。这是由于以下原因。

首先，当一个新的`goroutine`开始运行时，goroutine 调用立即返回。它不会等待 goroutine 完成执行。

其次，`main Goroutine`应该正在运行，以供任何其他`goroutines`运行。如果`main goroutine`终止，程序将被终止，其它`goroutine`将不再运行。

现在你明白它为什么没有输出`Hello World`了吧。在代码中，调用`go hello()`后，控制立即返回到下一行代码`fmt.Println("main function")`，而不等待`hello goroutine`结束。

您可以在`main goroutine`终止之前给它足够的时间来运行，从而执行`hello()`。

```
package main

import (
    "fmt"
    "time"
)

func hello(){
    fmt.Println("Hello World")
}

func main(){
    go hello()
    time.Sleep(1 * time.Second)
    fmt.Println("main function")

    //=> Hello World
    //=> main function
} 
```

Enter fullscreen mode Exit fullscreen mode

通常，用于阻塞`main goroutine`，直到所有其他`goroutines`完成它们的执行。

# 如何查看所有已有 goroutines 的数量

您可以使用`runtimeNumGoroutine()`获取代码中所有现有的 goroutines。

```
package main

import (
     "log"
     "runtime"
     "fmt"
    )

func hello(){
    fmt.Println("Hello World")
}

func main(){
    go hello()
    fmt.Println("main function")
    log.Println(runtime.NumGoroutine()) //-> 2018/10/09 07:39:07 2
} 
```

Enter fullscreen mode Exit fullscreen mode

# 通道

`Channels`为`goroutines`提供一种相互通信并同步执行的方式。数据可以从一个 goroutine 发送，也可以从另一个 goroutine 接收。

# 如何声明一个通道

可以像下面的代码一样声明一个`channel`。

```
ch := make(chan TYPE) 
```

Enter fullscreen mode Exit fullscreen mode

每个`channel`都有一个与之关联的类型。这种类型是允许信道传输的数据类型。您不能使用`channel`传输任何其他数据。

# 从一个通道发送和接收

您可以使用以下语法发送和接收来自`channel`的数据。

```
ch <- data // write to channel ch
variable := <- cd // read from channel ch 
```

Enter fullscreen mode Exit fullscreen mode

在第一行中，箭头指向`ch`，这意味着我们正在向通道`ch`写入数据。

在第二行，箭头从`ch`指向外，这意味着我们正在从`ch`读取数据并将数据存储到`variable`。

```
package main

import "fmt"

func main() {
  //Create a new channel
  ch := make(chan string)

  //Write data to ch
  go func() { ch <- "data" }()

  //Reading data from ch
  variable := <-ch
  fmt.Println(variable) //=> data
} 
```

Enter fullscreen mode Exit fullscreen mode

# 发送和接收默认都是阻断的

默认情况下，发送和接收数据块，直到另一端准备好。这意味着当一个数据被发送到一个通道时，控制被阻塞，直到其他某个`goroutine`从该通道读取。

这允许`goroutines`在没有显式锁或条件变量的情况下进行同步。

### 例 1

在下面的代码中，我创建了一个名为`ch`的布尔类型通道，并将一个函数声明为 goroutine。然后，我在函数中给`ch`传递了一个布尔值。

`<-ch`语句将阻塞代码，直到在`ch`通道上接收到一些布尔数据。

```
package main

import "fmt"

func main() {
  ch := make(chan bool)  //Create a boolean type channel

  //Run the following function as a goroutine.
  go func() {
    fmt.Println("Hello")

    ch <- true  //Pass a boolean value to the channel
  }()

  <- ch //Block until ch receives a boolean value
} 
```

Enter fullscreen mode Exit fullscreen mode

### 例 2

在这个例子中，我定义了一个名为`hello`的函数，它接受一个`channel`作为它的参数。然后，我在`main goroutine`中创建了一个名为`ch`的通道，并将其作为参数传递给`hello()` goroutine。

与[示例 1](#%20example1) 一样，`<-ch`语句将阻塞代码，直到在`ch`通道上接收到一些布尔数据。

```
package main

import "fmt"

func hello(channel chan bool) {  
    fmt.Println("Hello world")
    channel <- true
}
func main() {  
    ch := make(chan bool) //Create a boolean type channel

    go hello(ch) //Run hello method as a goroutine

    <-ch //Block until ch receives a boolean value
    fmt.Println("main function")

    //=> Hello world
    //   main function
} 
```

Enter fullscreen mode Exit fullscreen mode

# 更多资源

[围棋资源【并发】](https://www.golang-book.com/books/intro/10)
[围棋之旅(Goroutines)](https://tour.golang.org/concurrency/1)
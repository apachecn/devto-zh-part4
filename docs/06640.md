# 在 Golang 与 RabbitMQ 合作

> 原文：<https://dev.to/olushola_k/working-with-rabbitmq-in-golang-1kmj>

# 简介

你好。我是奥卢苏拉！我是后端软件工程师。我最近买了 Golang，我对它的功能非常感兴趣，主要是在速度方面。在本文中，我将使用 RabbitMQ 建立一个简单的 Golang 项目。

# 什么是 RabbitMQ？

RabbitMQ 是一个开源的消息代理，它使用 AMQP 的发布/订阅模式来促进服务之间的通信。

如果您熟悉 web 编程，那么您很有可能遇到过 REST(表述性状态转移)，它只是不同服务通过 HTTP(超文本传输协议)和请求/响应模型进行通信和交换信息的一种方式。

服务之间相互通信的另一种方式是使用消息代理，如 RabbitMQ，通过发布/订阅模式。发布者，嗯，发布消息和订阅者？你猜对了，订阅消息。

发布/订阅架构的一个优点是发布者和订阅者是松散耦合的，这意味着发布者不需要关心订阅者，反之亦然，消息通常是基于主题的。

# GoLang 工作区设置

为了在 Golang 项目中使用 rabbitMQ，我们需要一些基本的设置。

首先，我们将在开发机器上下载 Golang 并设置我们的工作空间。

*   这里下载 GoLang [并安装。](https://golang.org/dl/)
*   通过在您的终端中运行 *go 版本*来验证安装；你的 Golang 版本应该会得到回应。

```
➜ go version
go version go1.12.7 darwin/amd64 
```

Go 对我们工作空间的结构非常固执己见，但是设置工作空间却非常简单。

*   首先，在您的终端中运行 *go env* 并找到名为 GOPATH 的变量；它会显示 go 安装文件夹的位置。

```
➜ go env
...
GOPATH="/Users/olushola/go"
... 
```

*   导航到目录并创建两个子文件夹， **src** 和 **bin** 。
*   在 **src** 目录中，用您选择的版本控制软件创建一个子文件夹；我将使用 GitHub，所以我将创建一个名为 GitHub.com 的文件夹(对于 bitbucket，gitlab 分别使用 bitbucket.com，gitlab.com)。
*   导航到**GitHub.com**目录，用你的版本控制软件用户名创建一个子文件夹，给我它的 **shola-0507** 。(我们将在这个目录中创建我们的项目。)
*   对于本教程，我们将创建一个名为 **go_rabbit** 的目录。

我们的 Go 工作区结构应该是这样的。

```
-go
    -src
        -Github.com
            -shola-0507
                -go_rabbit
    -bin 
```

# 用 Docker 设置 RabbitMQ

我们可以用几种方法在我们的开发环境中设置 rabbitMQ 在本教程中，我们将使用 docker。如果你没有安装 docker，你可以创建一个帐号，在这里下载[。](https://www.docker.com/get-started)

在您的终端中，运行

```
➜ docker run --detach --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3-management 
```

这将创建一个带有 rabbitMQ 实例的 docker 容器。

在您的浏览器中，导航到 localhost:15672，您应该会看到 RabbitMQ UI 使用 *guest* 作为用户名和密码登录。

[![RabbitMQ User Interface](img/993a1f778cd57180961fab2f8980a10f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ev_pWIe2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1hdsgg31w53u0gzprykv.png)

# 用 RabbitMQ 连接并发布消息

现在我们已经设置了项目并运行了 rabbitMQ，我们将从 GoLang 项目连接到 rabbitMQ。

要执行连接，我们需要安装 GitHub.com/streadway/amqp 库。为此，在你的终端中运行*go get-u GitHub.com/streadway/amqp*。

```
➜ go get -u GitHub.com/streadway/amqp 
```

在 go_rabbit 目录中，创建一个 main.go 文件，这是我们放置所有 Golang 代码的地方。

首先，导入我们项目所需的依赖项，包括 amqp 库，并定义一个主函数。

```
package main

import (
    "fmt"
    "os"

    "github.com/streadway/amqp"
)

func main() {} 
```

接下来，我们将使用 [amqp 连接到 rabbitmq 代理。Dail()](https://godoc.org/github.com/streadway/amqp#Dial) 方法，该方法返回一个新的 TCP 连接和一个错误——如果连接成功，该错误将为 *nil* 。

```
func main() {
    // Get the connection string from the environment variable
    url := os.Getenv("AMQP_URL")

    //If it doesn't exist, use the default connection string.

    if url == "" {
        //Don't do this in production, this is for testing purposes only.
        url = "amqp://guest:guest@localhost:5672"
    }

    // Connect to the rabbitMQ instance
    connection, err := amqp.Dial(url)

    if err != nil {
        panic("could not establish connection with RabbitMQ:" + err.Error())
    }
} 
```

在 AMQP，直接在连接上执行操作不是好的做法。这是因为一些应用程序需要同时连接到代理，同时保持许多 TCP 连接打开会消耗系统资源，并使设置防火墙变得困难。

相反，我们使用[通道](https://www.rabbitmq.com/channels.html)。通道是共享单个 TCP 连接的轻量级连接。

为了创建通道，我们在连接上调用 channel 方法。

```
func main() {
    ...

    // Create a channel from the connection. We'll use channels to access the data in the queue rather than the connection itself.
    channel, err := connection.Channel()

    if err != nil {
        panic("could not open RabbitMQ channel:" + err.Error())
    }
} 
```

接下来，我们使用我们将用来发布消息的 [ExchangeDeclare](https://godoc.org/github.com/streadway/amqp#Channel.ExchangeDeclare) 方法声明一个名为*事件*的*主题*交换。

```
func main() {
    ...

    // We create an exchange that will bind to the queue to send and receive messages
    err = channel.ExchangeDeclare("events", "topic", true, false, false, false, nil)

    if err != nil {
        panic(err)
    }
} 
```

然后我们声明一个名为*测试*的[队列](https://godoc.org/github.com/streadway/amqp#Channel.QueueDeclare),[将*测试*队列](https://godoc.org/github.com/streadway/amqp#Channel.QueueBind)绑定到我们之前声明的*事件*交换，[通过*事件*将](https://godoc.org/github.com/streadway/amqp#Channel.Publish)数据发布到*测试*。

要发送到交换机的数据必须执行 [amqp。发布](https://godoc.org/github.com/streadway/amqp#Publishing)结构。

```
func main() {
    ...

    // We create a message to be sent to the queue. 
    // It has to be an instance of the aqmp publishing struct
    message := amqp.Publishing{
        Body: []byte("Hello World"),
    }

    // We publish the message to the exahange we created earlier
    err = channel.Publish("events", "random-key", false, false, message)

    if err != nil {
        panic("error publishing a message to the queue:" + err.Error())
    }

    // We create a queue named Test
    _, err = channel.QueueDeclare("test", true, false, false, false, nil)

    if err != nil {
        panic("error declaring the queue: " + err.Error())
    }

    // We bind the queue to the exchange to send and receive data from the queue
    err = channel.QueueBind("test", "#", "events", false, nil)

    if err != nil {
        panic("error binding to the queue: " + err.Error())
    }
} 
```

我们已经成功设置了我们的发布者。此时，我们的 main.go 文件应该是这样的。

```
package main

import (
    "fmt"
    "os"

    "github.com/streadway/amqp"
)

func main() {
    // Get the connection string from the environment variable
    url := os.Getenv("AMQP_URL")

    //If it doesnt exist, use the default connection string
    if url == "" {
        url = "amqp://guest:guest@localhost:5672"
    }

    // Connect to the rabbitMQ instance
    connection, err := amqp.Dial(url)

    if err != nil {
        panic("could not establish connection with RabbitMQ:" + err.Error())
    }

    // Create a channel from the connection. We'll use channels to access the data in the queue rather than the
    // connection itself
    channel, err := connection.Channel()

    if err != nil {
        panic("could not open RabbitMQ channel:" + err.Error())
    }

    // We create an exahange that will bind to the queue to send and receive messages
    err = channel.ExchangeDeclare("events", "topic", true, false, false, false, nil)

    if err != nil {
        panic(err)
    }

    // We create a message to be sent to the queue.
    // It has to be an instance of the aqmp publishing struct
    message := amqp.Publishing{
        Body: []byte("Hello World"),
    }

    // We publish the message to the exahange we created earlier
    err = channel.Publish("events", "random-key", false, false, message)

    if err != nil {
        panic("error publishing a message to the queue:" + err.Error())
    }

    // We create a queue named Test
    _, err = channel.QueueDeclare("test", true, false, false, false, nil)

    if err != nil {
        panic("error declaring the queue: " + err.Error())
    }

    // We bind the queue to the exchange to send and receive data from the queue
    err = channel.QueueBind("test", "#", "events", false, nil)

    if err != nil {
        panic("error binding to the queue: " + err.Error())
    }
} 
```

我们可以通过在项目目录中运行 *go run main.go* 来测试我们的代码。

```
➜ go run main.go 
```

如果一切按预期运行，我们应该有一个包含 *Hello，World* 消息的测试队列。

[![Test Queue With Published Data](img/aa29aa10b8cb4c10e9497ce962d87239.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Nsv1Me0E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lpmiyaytpu3nwlkvs2h0.png)

# 消耗队列中的消息

既然我们在*测试*中有了数据，我们将使用队列中的数据并将其记录到控制台。

在我们的 main.go 文件中，我们将使用 [consume](https://godoc.org/github.com/streadway/amqp#Channel.Consume) 方法在*测试*中获取消息(数据)，遍历消息，将消息体打印到控制台，[确认](https://godoc.org/github.com/streadway/amqp#Channel.Ack)消息。

```
func main() {
    ...

    // We consume data in the queue named test using the channel we created in go.
    msgs, err := channel.Consume("test", "", false, false, false, false, nil)

    if err != nil {
        panic("error consuming the queue: " + err.Error())
    }

    // We loop through the messages in the queue and print them to the console.
    // The msgs will be a go channel, not an amqp channel
    for msg := range msgs {
    //print the message to the console
        fmt.Println("message received: " + string(msg.Body))
    // Acknowledge that we have received the message so it can be removed from the queue
        msg.Ack(false)
    }

    // We close the connection after the operation has completed.
    defer connection.Close()
} 
```

现在 main.go 文件应该是这样的。

```
package main

import (
    "fmt"
    "os"

    "github.com/streadway/amqp"
)

func main() {
    // Get the connection string from the environment variable
    url := os.Getenv("AMQP_URL")

    //If it doesnt exist, use the default connection string
    if url == "" {
        url = "amqp://guest:guest@localhost:5672"
    }

    // Connect to the rabbitMQ instance
    connection, err := amqp.Dial(url)

    if err != nil {
        panic("could not establish connection with RabbitMQ:" + err.Error())
    }

    // Create a channel from the connection. We'll use channels to access the data in the queue rather than the
    // connection itself
    channel, err := connection.Channel()

    if err != nil {
        panic("could not open RabbitMQ channel:" + err.Error())
    }

    // We create an exahange that will bind to the queue to send and receive messages
    err = channel.ExchangeDeclare("events", "topic", true, false, false, false, nil)

    if err != nil {
        panic(err)
    }

    // We create a message to be sent to the queue.
    // It has to be an instance of the aqmp publishing struct
    message := amqp.Publishing{
        Body: []byte("Hello World"),
    }

    // We publish the message to the exahange we created earlier
    err = channel.Publish("events", "random-key", false, false, message)

    if err != nil {
        panic("error publishing a message to the queue:" + err.Error())
    }

    // We create a queue named Test
    _, err = channel.QueueDeclare("test", true, false, false, false, nil)

    if err != nil {
        panic("error declaring the queue: " + err.Error())
    }

    // We bind the queue to the exchange to send and receive data from the queue
    err = channel.QueueBind("test", "#", "events", false, nil)

    if err != nil {
        panic("error binding to the queue: " + err.Error())
    }

    // We consume data from the queue named Test using the channel we created in go.
    msgs, err := channel.Consume("test", "", false, false, false, false, nil)

    if err != nil {
        panic("error consuming the queue: " + err.Error())
    }

    // We loop through the messages in the queue and print them in the console.
    // The msgs will be a go channel, not an amqp channel
    for msg := range msgs {
        fmt.Println("message received: " + string(msg.Body))
        msg.Ack(false)
    }

    // We close the connection after the operation has completed.
    defer connection.Close()
} 
```

完成所有设置后，我们可以通过在项目目录中运行 *go run main.go* 来测试我们的代码，并期待控制台中出现“message received: Hello World”响应。

```
➜ go run main.go
message received: Hello World 
```

我们现在有一个可以与 RabbitMQ 通信的 Golang 项目！

感谢阅读！
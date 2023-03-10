# Rust Nightly Telnet Echo 服务器示例

> 原文：<https://dev.to/jtenner/rust-nightly-tokio-codecs-21f1>

Rust 是一门很难学的语言，但是它在一次大部分开发人员称之为“苦差事”的考验中教会了你。学习如何在`Future` s 中使用最新的`async`语法的确是一个挑战，但是我现在真的很喜欢在 rust 中工作。

在这篇博文中，我用一小段叙述描述了一个简单的 telnet echo 服务器的每个部分，记录了我写的源代码。希望我也能对开发过程提供一些反馈，因为 rust 中的异步功能太新了。

因此，让我们分解设置一个 tcp 服务器来发送和接收 telnet 事件。这当然是微不足道的任务，只要你有一个编解码器，并正在与出血边缘生锈。ink 真正的挑战是使用最少的文档和夜间信任文档。

在下面的例子中，我们使用了一个`TelnetCodec`(还没有开源！)现在我们可以把它当作一个黑盒，为处理 telnet 事件提供一个抽象层。

```
use tokio::net::TcpListener;
use tokio::prelude::*;
use tokio::codec::Decoder;

mod telnet_codec;
use telnet_codec::*;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
  let mut listener = TcpListener::bind("127.0.0.1:7000").await?;
  println!("listening on port 7000");
  loop {
    let (socket, _) = listener.accept().await?;
    tokio::spawn(async move {
      let codec = TelnetCodec::new(4096_u16);
      let (mut sink, mut input) = codec.framed(socket).split();
      while let Some(Ok(event)) = input.next().await {
        println!("Event {:?}", event);
        match event {
          TelnetEvent::Message(value) => {
            println!("Echoing message: {}", value);
            if let Err(error) = sink.send(TelnetEvent::Message(value)).await {
              println!("An error occured {}", error);
            }
          },
          _ => {
            // nop
          }
        }
      }
    });
  }
} 
```

这个例子非常简单，使用 tokio 的旧版本的类似例子要复杂得多，但是在这个例子中，很容易看出发生了什么。

让我们一行一行地分解它。

```
#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> { 
```

Tokio 定义了一个指令，让我们将应用程序入口点标记为`async`。它像一个装饰器一样工作，所有连接常规`main()`函数的细节都委托给 tokio 本身。

```
let mut listener = TcpListener::bind("127.0.0.1:7000").await?; 
```

这里，我们创建一个绑定到端口`7000`的 tcp 监听器。关于这一行有趣的部分是使用新的`await`语法的最后一部分。它看起来像一个属性访问，而不是一个第一类关键字，但是，它仍然做同样的事情。它分派一个将要完成的任务，并返回一个`Result<(), Box<dyn std::error::Error>>`。如果出现错误，进程将退出，并显示错误。如果存在错误，表达式末尾的`?`操作符将导致`main()`函数返回错误。该错误将显示在 stderr 上。

```
loop {
  let (socket, _) = listener.accept().await?;
  tokio::spawn(async move { 
```

现在，主任务只是等待并轮询监听器接受 tcp 连接。

使用`.await?`语法从未来提取套接字，然后将它`move`到`async`闭包中。使用一个`async move`闭包允许我们定义一个被称为`Future`的任务，它将由 tokio 反应堆管理。在`Tokio::spawn`被调用后，每当 cpu 可用时，它将添加这个新创建的 future 进行处理。

此时，我们需要一种方法来处理传入的 tcp 连接。特别是，我们将使用一个`Codec`来处理传入的字节。需要使用编解码器提供的`Encoder`和`Decoder`来解析每个传入的字节。

```
let codec = TelnetCodec::new(4096_u16); 
```

这个特定的编解码器要求每个套接字都有一个底层缓冲区，所以传递它`4096`会通知`TelnetCodec`在截断消息之前，它应该只保留`4096`字节。

至于实际的 telnet 消息，编解码器提供了一个封装了所有消息类型的`enum`。这种封装使得使用`TelnetEvent`枚举值与 tcp 套接字通信成为可能。

```
#[derive(Debug,PartialEq)]
pub enum TelnetEvent {
  Do(TelnetOption),
  Dont(TelnetOption),
  Will(TelnetOption),
  Wont(TelnetOption),
  Subnegotiation(TelnetOption, Vec<u8>),
  Message(String),
  Nop,
} 
```

如果您碰巧熟悉 telnet 的编码方式，那么您应该对这个事件列表很熟悉。简而言之，`Message`事件将虚拟终端的 stdin 和 stdout 字节发送到客户端或服务器。为了返回“帧”(或`TelnetEvent`枚举对象)，我们要求编解码器取得套接字的所有权，并让它使用`Stream`处理解析输入，使用`Sink`发送输出。调用`.split()`将返回与此编解码器相关联的`Sink`和`Stream`，因此它们可以分开。

```
let (mut sink, mut input) = codec.framed(socket).split(); 
```

为了获得传入的事件，我们再次使用`.await`语法，这一次手动处理错误，因为当错误发生时，连接并不总是需要关闭。

```
while let Some(Ok(event)) = input.next().await { 
```

接下来，匹配事件并将它们记录到 stdout 中。

```
println!("Event {:?}", event);
match event {
  TelnetEvent::Message(value) => { 
```

为了回应`Message`事件，我们将`value`字符串传递给`Message`事件中的接收器。向接收器发送一个`TelnetEvent`将返回一个 future，因为发送将异步发生。同样，我们使用`.await`语法检查来查看是否发生了错误。

```
if let Err(error) = sink.send(TelnetEvent::Message(value)).await { 
```

最后，使用`println!`记录发生的任何错误。就是这样！现在，剩下的就是处理传入的事件流。

这里总结了如何使用最新的 tokio 设置 Tcp telnet 服务器。本系列的下一篇博文将介绍如何创建编解码器，以及它们是什么。

感谢阅读！
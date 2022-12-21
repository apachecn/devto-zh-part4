# 创建 Tokio 编解码器

> 原文：<https://dev.to/jtenner/creating-a-tokio-codec-1f0l>

今天，我很自豪地宣布，我已经使用 rust nightly 和 async/await 从头开始编写了一个 Tokio 编解码器。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[jtenner](https://github.com/jtenner)/[telnet _ codec](https://github.com/jtenner/telnet_codec)

<article class="markdown-body entry-content container-lg" itemprop="text">

# telnet _ 编解码器

一个用 rust 写的 tokio telnet 编解码器实现。

</article>

[View on GitHub](https://github.com/jtenner/telnet_codec)

Tokio 是一个异步套接字服务器框架，可以帮助您快速启动和运行 web 服务器。tokio 帮助开发人员创建 web 服务器的主要方式之一是给他们一个框架，通过这个框架可以使用专门的编解码器。编解码器是一种将流信息转换成可管理的块(或更好地称为“帧”)的特殊方式

例如，以下示例显示了如何使用 telnet 编解码器来制作 echo 服务器。

```
use telnet_codec::codec::TelnetCodec;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
  let mut listener = TcpListener::bind("127.0.0.1:7000").await?;
  println!("listening on port 7000");
  loop {
    let (socket, _) = listener.accept().await?;
    tokio::spawn(async move {
      // The telnet codec has an internal buffer used for each connection.
      // This codec requires the buffer size to be specified up front (4096 bytes)
      let codec = TelnetCodec::new(4096_usize);
      let (mut sink, mut input) = codec.framed(socket).split();
      // Use the sink to write telnet events to the socket.
      // use the input to iterate over telnet events.
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

本质上，开发人员将接受一个连接，并将其传递给选择的编解码器，该编解码器将接管为您解释入站和出站 IO 的责任。

当实现一个定制的编解码器时，在内部它需要在结构上实现一个`Encoder`和`Decoder`特征。`Encoder`特征定义了一种获取“帧”并将其转换成字节的方法，而`Decoder`定义了一种将字节转换成一组`Frames`的方法。

我开发`TelnetCodec`结构的第一件事是运行`cargo init --lib`并修改`Cargo.toml`文件以包含`tokio`最新版本和`bytes`包。不要忘记每晚使用最新的锈 tokio！

```
[package]
name = "telnet_codec"
version = "0.0.0"
authors = ["Joshua Tenner <tenner.joshua@gmail.com>"]
edition = "2018"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
tokio = "=0.2.0-alpha.4"
bytes = "0.4.12" 
```

## 实现编码器

接下来，我用这个定义实现了`Encoder`特征。

```
use tokio::codec::{ Encoder };

impl Encoder for TelnetCodec {
  type Item = TelnetEvent;
  type Error = TelnetError;

  fn encode(&mut self, event: Self::Item, buf: &mut BytesMut) ->
    Result<(), Self::Error> {
    // do something here
  }
} 
```

记住，`Encoder`的工作是将`TelnetEvent`转换成字节，所以`encode()`函数必须有三个参数。这些参数包括对`&mut self`的引用、telnet 事件本身以及包含需要写入的字节的缓冲区。

telnet 编码任务简单明了。通常，telnet 服务器会向连接的客户端发送`DO`、`DONT`、`WILL`或`WONT`命令。这些命令只需要对三个字节的信息进行编码，并且可以用一个简单的`match`表达式来实现。

```
 fn encode(&mut self, event: TelnetEvent, buf: &mut BytesMut) -> Result<(), Self::Error> {
      match event {
        // basic commands are IAC (COMMAND) (OPT)
        TelnetEvent::Do(opt) => {
          buf.reserve(3); // allocate 3 bytes to be written
          buf.put(IAC); // send the Interperet as Command byte (255)
          buf.put(DO); // send the command (253)
          buf.put::<u8>(opt.into()); // send the option
        },
        TelnetEvent::Dont(opt) => {
          buf.reserve(3);
          buf.put(IAC);
          buf.put(DONT);
          buf.put::<u8>(opt.into());
        },
        TelnetEvent::Will(opt) => {
          buf.reserve(3);
          buf.put(IAC);
          buf.put(WILL);
          buf.put::<u8>(opt.into());
        },
        TelnetEvent::Wont(opt) => {
          buf.reserve(3);
          buf.put(IAC);
          buf.put(WONT);
          buf.put::<u8>(opt.into());
        },
        // another event
        _ => { }
      }
  Ok(())
} 
```

由于每个命令的模式都是相同的，因此可以组合匹配臂。遗憾的是，我对 rust 没有足够的经验来找到一个好的方法。如果*你*能想出更好的办法，请随时提交拉取请求，或者在下面评论(请！)

一旦函数返回`Ok(())`，`BytesMut`缓冲区中的字节将被发送到客户端。当然，还有其他可以实现的选项和事件，它们目前以工作方式存在于 [telnet 编解码器 repo](https://github.com/jtenner/telnet_codec/blob/master/src/codec.rs) 中。

## 实现解码器

接下来，我实现了解码器。解码器的工作是解码输入的字节，这些字节可能被分成许多不同的帧。

这是一项相当复杂的任务，我没有写一篇关于如何解释传入的 telnet 字节的教程，而是选择记录一个解码器功能如何工作，以及如何手动测试它。

这是一个如何为编解码器实现`Decoder`特征的例子。

```
use tokio::codec::{ Decoder };

impl Decoder for TelnetCodec {
  type Item = TelnetEvent;
  type Error = TelnetError;

  fn decode(&mut self, src: &mut BytesMut) -> Result<Option<Self::Item>, Self::Error> {
    // do something with the incoming src bytes
  }
} 
```

反复调用`decode`函数，直到它返回`Ok(None)`从给定的缓冲区中收集所有相关的“帧”。因此，`decode()`函数本身负责返回一个“帧”,然后将`BytesMut`对象截断到下一帧的开始，有效地一次通过一个函数调用迭代整个缓冲区。解码器逻辑遵循一些规则。

1.  如果流还没有提供足够的字节，或者是空的，解码器应该返回`Ok(None)`来停止解析帧
2.  如果该帧无效，则消耗所有错误字节并返回`Err(TelnetError::ErrorKind)`以向消费者报告一个`Error`帧
3.  如果字节有效，则消耗当前帧中的所有字节并返回`Ok(Some(TelnetEvent::EventKind))`

下面是一个简单的例子`decode()`函数。

```
 fn decode(&mut self, src: &mut BytesMut) -> Result<Option<Self::Item>, Self::Error> {
    let len = src.len();
    if len == 0 {
      // there are no bytes to consume, stop querying the buffer
      return Ok(None); 
    }

    // parse out the bytes from the start of the buffer

    // then check to see if an error occured
    if invalid {
      // src should be truncated to [next_start_index,len)
      src.split_to(next_start_index); 
      return Err(TelnetError::ErrorKind);
    }

    // truncate src here too
    src.split_to(next_start_index);
    return Ok(Some(TelnetEvent::EventKind));
  } 
```

## 测试解码器

为了测试一个解码器，迭代每个函数调用的输出，并将结果推送到一个`Vec<Result<Option<TelnetEvent>, TelnetError>>`。这里是我写的一个定制的`consume`函数，用来测试我的解码器的输出。

```
 fn consume(codec: &mut TelnetCodec, bytes: &mut BytesMut) -> Vec<Result<Option<TelnetEvent>, TelnetError>> {
        let mut result = Vec::new();
        loop {
            match codec.decode(bytes) {
                Ok(None) => { break; }
                output => result.push(output)
            }
        }
        return result;
    } 
```

现在可以编写一些测试来验证实现了。

我喜欢使用的测试策略如下:

1.  创建一些国家
2.  给某人动手术
3.  将输出与一些预期值进行比较

比如这是`telnet_codec`回购中的“Hello World”解码器测试。

```
 #[test]
    fn finished_message() {
        // This test validates that the codec converts the byte input
        // into TelnetEvents correctly.
        // First, instantiate the codec.
        let mut codec = TelnetCodec::new(4096);
        // Then create a BytesMut buffer from some bytes to be decoded.
        let mut bytes = BytesMut::from(b"Hello world\r\n".to_vec());
        // Finally consume the input bytes, and compare the frames that
        // that the decode function returns.
        let result = consume(&mut codec, &mut bytes);

        // the bytes should be completely consumed, so `bytes.len()`
        // should be 0
        assert_eq!(bytes.len(), 0_usize);

        // Since we sent a message to the decoder that ends in "\r\n",
        // it should return a single telnet frame in the form of a
        // message event that contains the expected String value.
        assert_eq!(
            result,
            vec![
                // Decode returns Result<Option<TelnetEvent>, TelnetError>
                Ok(Some(TelnetEvent::Message(String::from("Hello world"))))
            ],
        );
    } 
```

## 测试编码器

我们还可以测试编码器，以验证输出字节是我们所期望的。这是一个“Hello world”编码器示例测试。

```
 #[test]
    fn message_encode() {
        // The encoder is responsible for turning TelnetEvents into
        // byte frames. First, create the codec.
        let mut codec = TelnetCodec::new(4096);
        // Next, create a buffer to be written to.
        let mut output = BytesMut::new();
        // Finally, create the message event and encode the result.
        let message = TelnetEvent::Message(String::from("Hello world!\r\n"));

        // encode the message and read the output
        codec.encode(message, &mut output).expect("Invalid encoding sequence");

        // utf8 output
        assert_eq!(
            output,
            // The output should have the following bytes in the buffer
            BytesMut::from(vec![
                0x48, 0x65, 0x6c, 0x6c, 0x6f, 0x20, 0x77, 0x6f, 0x72, 0x6c, 0x64, 0x21, 0x0d, 0x0a,
            ]),
        );
    } 
```

编码和解码显然不是一项简单的任务，但是在测试编解码器时，使用工具来帮助测试这些功能是非常值得的。请随时在下面提问，或者修正我愚蠢的编码错误。我一直在学习如何使用 rust，我计划维护这个编解码器，这样其他人也可以使用它。

感谢您的阅读！
# 开始使用 rocket

> 原文：<https://dev.to/wpanas/getting-started-with-rsocket-4h5d>

在本文中，我将向您展示如何在 Spring Boot 中使用 RSocket。

# r socket 是什么？

RSocket 是一种双向消息驱动的通信协议。它具有超越标准请求/响应的高级通信流程。你可以用一条消息、一串消息来回应，或者根本不回应。服务器还可以在与客户端建立的通道上开始通信。

RSocket 允许您使用下列传输协议进行通信:

*   传输控制协议（Transmission Control Protocol）
*   WebSocket
*   氩
*   HTTP/2 流

如果你想了解更多，可以观看本·黑尔 2019 年春季 I/0 的[演讲](https://www.youtube.com/watch?v=dGNv-Djm7h0)。

# RSocket TCP 服务器

RSocket 服务器是整个项目中最简单的部分。你需要做的就是去 [Spring Initializr](https://start.spring.io) 选择 Spring Boot，至少 2.2.x 版本，然后选择 RSocket 作为依赖。

要使用 TCP 协议配置 RSocket 服务器，请在`application.properties`中添加以下行。该配置将在端口 7000 打开 RSocket。

```
spring.rsocket.server.transport=tcp
spring.rsocket.server.port=7000 
```

所需的最后一步是添加一些控制器，您就可以使用服务器了。你可以试试这样的东西:

```
@Controller
class ServerController {
    @MessageMapping("queue")
    fun helloQueue(incomingMessage: Message): Mono<Message> 
        = Mono.just(Message("Hello ${incomingMessage.message}"))
}

data class Message(val message: String) 
```

# RSocket TCP 客户端

客户端应用程序非常相似。进入[弹簧初始化](https://start.spring.io)，用`RSocket`和`Spring Reactive Web`引导。

为了通过 TCP 端口`7000`与 RSocket 通信，您需要创建一个`RSocketRequester`。对于 TCP 协议，它可能是这样的。

```
@Configuration
class ClientConfiguration {
    @Bean
    internal fun rSocketRequester(rSocketStrategies: RSocketStrategies): RSocketRequester 
        = RSocketRequester.builder()
            .rsocketStrategies(rSocketStrategies)
            .connect(TcpClientTransport.create(7000))
            .block()!!
} 
```

您的客户端应用程序可以是使用 HTTP 的标准反应式 web 应用程序。若要测试与已配置的服务器的通信，请添加以下端点。

```
@RestController
class ClientController(private val rSocketRequester: RSocketRequester) {
    @GetMapping("/hello/{name}")
    fun hello(@PathVariable("name") name: String): Mono<String> = rSocketRequester.route("queue")
        .data(Message(name))
        .retrieveMono(Message::class.java)
        .map { it.message }
}

data class Message(val message: String) 
```

# RSocket WebSocket 服务器

WebSocket 要求您向服务器应用程序添加`Spring Reactive Web`依赖项。RSocket 端口将与 Web 应用程序端口相同。您需要为 RSocket 端点定义映射，而不是配置端口。在这个例子中，您将使用`/rsocket`路径。在`application.properties`文件中配置以下属性。

```
spring.rsocket.server.transport=websocket
spring.rsocket.server.mapping-path=/rsocket 
```

您为 TCP 服务器创建的控制器不需要更改。

# RSocket WebSocket 客户端

您的服务器可能已经在端口`8080`启动。在`application.properties`中将客户端端口更改为另一个端口，即`8081`。

```
server.port=8081 
```

使用 WebSocket 传输协议的客户端配置与上一个类似。你需要做的就是提供`ClientTransport`。

```
@Configuration
class ClientConfiguration {
    @Bean
    internal fun rSocketRequester(rSocketStrategies: RSocketStrategies): RSocketRequester {
        val uri = URI.create("ws://localhost:8080/rsocket")
        return RSocketRequester.builder()
            .rsocketStrategies(rSocketStrategies)
            .connect(WebsocketClientTransport.create(uri))
            .block()!!
    }
} 
```

您为 TCP 客户端创建的控制器不需要更改。

# 总结

Spring Boot 简化了创建使用 RSocket 应用程序。我希望这些例子能告诉你如何去做。欲了解更多关于 about RSocket 的信息，请访问官方参考指南。
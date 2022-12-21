# Corda -将 Ktor web 服务器连接到 Corda 节点

> 原文：<https://dev.to/lankydandev/corda-connecting-a-ktor-web-server-to-a-corda-node-1k3j>

这篇博文的准备工作在几周前就开始了(可能已经一个多月了)。在我写关于将 Corda 和 Ktor 融合在一起的文章之前，我首先需要做好基础工作，并且只关注 Ktor。这就是我的博客 [Ktor - a Kotlin web 框架](https://lankydan.dev/ktor-a-kotlin-web-framework)诞生的地方。如果你以前没有用过或看过 Ktor，我建议你在阅读这篇文章之前或之后浏览一下这篇文章。提前阅读可能是一个更好的主意，但是你可以掌控自己的生活🤷。

这篇文章将着重于实现一个连接到 Corda 节点的 Ktor web 服务器。我不打算谈论你为什么应该使用 Ktor。这个决定取决于你。我所做的是给你提供一些信息，让你自己做出决定(就像你在网上读到的任何东西一样)🙄).

## 依赖关系

```
buildscript {
  ext.ktor_version = '1.2.2'
  ext.kotlin_version_for_app = '1.3.41'

  repositories {
    mavenCentral()
    jcenter()
  }

  dependencies {
    classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version_for_app"
  }
}

apply plugin: 'java'
apply plugin: 'kotlin'

java {
  disableAutoTargetJvm()
}

dependencies {
  compile "org.jetbrains.kotlin:kotlin-stdlib-jdk8:$kotlin_version_for_app"
  compile "$corda_release_group:corda-jackson:$corda_release_version"
  compile "$corda_release_group:corda-rpc:$corda_release_version"
  compile "$corda_release_group:corda:$corda_release_version"
  compile "io.ktor:ktor-server-netty:$ktor_version"
  compile "ch.qos.logback:logback-classic:1.2.3"
  implementation ("io.ktor:ktor-jackson:$ktor_version") {
    exclude group: 'com.fasterxml.jackson.module', module: 'jackson-module-kotlin'
  }
  compile project(":contracts")
  compile project(":workflows")
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有几件事需要强调。首先是`kotlin_version_for_app`属性。Ktor 需要 Kotlin 1.3(因为它使用协程)，但是在撰写本文时，Corda 只支持 Kotlin 1.2。因此，web 服务器代码和 Corda 节点需要使用不同的版本。第二，`jackson-module-kotlin`被排除，因为它由于版本不匹配而导致运行时错误。

## 高水平看代码

下面是一小段代码，显示了 web 服务器实现的起点:

```
fun main() {
  embeddedServer(
    Netty,
    port = System.getProperty("server.port").toInt(),
    module = Application::module
  ).start().addShutdownHook()
}

fun Application.module() {
  val connection: CordaRPCConnection = connectToNode()
  install(CallLogging) { level = Level.INFO }
  install(ContentNegotiation) { cordaJackson(connection.proxy) }
  routing { messages(connection.proxy) }
  addShutdownEvent(connection)
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码将所有内容联系在一起，因为服务器的所有功能都是从上面的功能中派生出来的。

`module`的内容将在以下章节中探讨。

## 连接到节点

我打赌你可能很清楚`connectToNode`是做什么的。不管怎样，我希望你这样做…以下是`connectToNode` :
的内容

```
fun connectToNode(
  host: String = System.getProperty("config.rpc.host"),
  rpcPort: Int = System.getProperty("config.rpc.port").toInt(),
  username: String = System.getProperty("config.rpc.username"),
  password: String = System.getProperty("config.rpc.password")
): CordaRPCConnection {
  val rpcAddress = NetworkHostAndPort(host, rpcPort)
  val rpcClient = CordaRPCClient(rpcAddress)
  return rpcClient.start(username, password)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您看过任何 Corda 示例，那么您可能会对这段代码很熟悉。长话短说，它用给定的连接细节连接到节点。我选择从应用程序的系统属性中生成函数的默认参数。这个实现不是特别重要，它只是连接到节点，可以用几种不同的方式编写。

从该函数返回一个`CordaRPCConnection`。最初，我想返回一个`CordaRPCOps`,因为连接本身不会做太多事情。但是，如果不返回连接，就没有办法正常地断开与节点的连接。换句话说，需要有一种方法在服务器停止时调用`notifyServerAndClose`。这将在后面的文章中进一步探讨。

## 设置杰克逊

需要做一些额外的设置来正确地使用 Jackson 和 Corda:

```
fun ContentNegotiation.Configuration.cordaJackson(proxy: CordaRPCOps) {
  val mapper: ObjectMapper = JacksonSupport.createDefaultMapper(proxy)
  mapper.apply {
    setDefaultPrettyPrinter(DefaultPrettyPrinter().apply {
      indentArraysWith(DefaultPrettyPrinter.FixedSpaceIndenter.instance)
      indentObjectsWith(DefaultIndenter(" ", "\n"))
    })
  }
  val converter = JacksonConverter(mapper)
  register(ContentType.Application.Json, converter)
} 
```

Enter fullscreen mode Exit fullscreen mode

Corda `ObjectMapper`用`createDefaultMapper`初始化，允许像`Party`或`X509Certificate`这样的类被序列化或反序列化。这可能很重要，取决于从您自己的 API 返回什么。

其余的代码是从`ktor-jackson`模块中窃取的。它稍微改变了 JSON 的输出，使之更加令人满意。

## 创建端点

HTTP 请求被路由到这些端点:

```
fun Routing.messages(proxy: CordaRPCOps) {
  route("/messages") {
    get("/") {
      call.respond(
        HttpStatusCode.OK,
        proxy.vaultQueryBy<MessageState>().states.map { it.state.data })
    }
    post("/") {
      val received = call.receive<Message>()
      try {
        val message = proxy.startFlow(
          ::SendMessageFlow,
          state(proxy, received, UUID.randomUUID())
        ).returnValue.getOrThrow().coreTransaction.outputStates.first() as MessageState
        call.respond(HttpStatusCode.Created, message)
      } catch (e: Exception) {
        call.respond(HttpStatusCode.InternalServerError, e.message ?: "Something went wrong")
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

从逻辑上来说，这里没有太多的东西。对于这段代码的重点解释，我推荐阅读前面提到的 [Ktor - a Kotlin web framework](https://lankydan.dev/ktor-a-kotlin-web-framework) 。

## 优雅地从一个节点断开

为了优雅地断开与节点的连接，web 服务器需要调用`CordaRPCConnection.notifyServerAndClose`。实现这一点需要一些我没有预料到的工作。下面是触发`notifyServerAndClose` :
的代码

```
fun NettyApplicationEngine.addShutdownHook() {
  Runtime.getRuntime().addShutdownHook(Thread {
    stop(1, 1, TimeUnit.SECONDS)
  })
  Thread.currentThread().join()
}

fun Application.addShutdownEvent(connection: CordaRPCConnection) {
  environment.monitor.subscribe(ApplicationStopped) {
    connection.notifyServerAndClose()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

服务器上添加了一个关机挂钩。正如在[Ktor 应用程序](https://dev.to/viniciusccarvalho/graceful-shutdown-of-ktor-applications-1h53)的正常关闭中所解释的，订阅`ApplicationStopped`事件不足以在终止应用程序时执行代码。关机钩子调用`stop`来优雅地关闭运行服务器的`NettyApplicationEngine`。导致关机事件被正确触发和执行。

## 仅此而已

是的，真的，就是这样。实现一个超级基本的 web 服务器根本不需要太多代码。真的没有什么可写的了。我已经向您展示了另一个 web 框架，它可以用来连接到 Corda 节点。您不必因为 Corda 示例使用 Spring 就将其默认为 Spring。如果你喜欢 Ktor，就用 Ktor。如果没有，就不要。如果你确实喜欢 Ktor 的外观，如果你还没有，我推荐你看一下[Ktor——一个 Kotlin web 框架](https://lankydan.dev/ktor-a-kotlin-web-framework)。

由于我将重点放在实现的更重要的方面，所以这篇文章中排除了很多代码。如果你对剩下的代码感兴趣，可以在我的 [GitHub](https://github.com/lankydan/corda-ktor-webserver) 上找到。

如果你喜欢这篇文章或者觉得它有帮助(或者两者兼而有之),那么请随时在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) ,并且记得与其他任何可能发现这篇文章有用的人分享！
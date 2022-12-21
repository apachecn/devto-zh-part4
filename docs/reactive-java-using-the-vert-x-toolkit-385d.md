# 使用 Vert.x 工具包的反应式 Java

> 原文：<https://dev.to/kabisasoftware/reactive-java-using-the-vert-x-toolkit-385d>

[Vert.x](https://vertx.io/) 是一个在 JVM 上开发反应式应用程序的工具包。尽管可以在许多不同的语言中使用 vert . x(Java、JavaScript、Groovy、Ruby、Ceylon、Scala *和* Kotlin ),这篇文章将使用普通的 Java。

[反应宣言](https://www.reactivemanifesto.org/)指出反应系统是:

*   反应灵敏，
*   有弹性，
*   有弹性，并且
*   消息驱动。

在我们考虑这在 Vert.x 的上下文中意味着什么之前，让我们看一个使用 Vert.x 的最简单的应用程序:

```
 1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23

package nl.kabisa.vertx;

import io.vertx.core.AbstractVerticle;
import io.vertx.core.Vertx;
import io.vertx.core.http.HttpServerOptions;

public class Application {

    private static class HelloWorldVerticle extends AbstractVerticle {

        @Override
        public void start() {
            var options = new HttpServerOptions().setPort(8080);
            vertx.createHttpServer(options)
                    .requestHandler(request -> request.response().end("Hello world"))
                    .listen();
        }
    }

    public static void main(String[] args) {
        Vertx.vertx().deployVerticle(new HelloWorldVerticle());
    }
} 
```

当运行这个应用程序时，当执行语句`Vertx.vertx().deployVerticle(new HelloWorldVerticle());`时，部署一个单独的顶点。这个顶点是类`HelloWorldVerticle`的一个实例。每个顶点都有一个`start`和一个`stop`方法。部署 verticle 时调用`start`方法，取消部署 verticle 时调用`stop`方法。在这个例子中，我们只提供了一个`start`方法的实现，并重用了类`AbstractVerticle`的(empty) `stop`方法。当部署`HelloworldVerticle`的实例时，创建一个 HTTP 服务器，它在端口`8080`上监听传入的请求。每个请求都会得到纯文本响应“Hello world”。

## 反应灵敏

默认情况下，Vert.x 为每个 CPU 内核创建两个线程来部署如上图所示的垂直系统。每个顶点被分配给一个特定的线程，该顶点的所有处理程序都在该线程上顺序执行。对于上面的例子，这意味着处理程序`request -> request.response().end("Hello world")`总是在同一个线程上执行。

因为给定 vertical 的处理程序从不并发执行，所以您不必担心锁定或与单个 vertical 相关的动作的原子性。然而，同一个 verticle 的多个实例，*可以让*同时执行它们的处理程序。事实上，这适用于任何两个垂直方向。这意味着，如果两个垂直市场共享一个资源，您可能仍然需要担心对该资源的并发访问。

作为开发人员，您有责任确保处理程序不能占用分配给它的线程太长时间。如果你阻塞一个线程太久，Vert.x 会记录一个警告。Vert.x 开发人员认为确保 Vert.x API 调用不会阻塞线程是他们的责任。因此，一个设计良好的 Vert.x 应用程序可以只用几个线程处理大量事件，最终使这样的应用程序*响应迅速*。

## 消息驱动和弹性

下面的例子显示了一个由两个垂直部分组成的应用程序。它说明了 Vert.x 的事件总线。事件总线允许您向任意数量的感兴趣的接收者广播消息，以及向单个接收者发送消息。广播的消息在每个注册了地址的接收者处结束，而直接发送的消息在单个接收者处结束。

在下面的例子中，`WorldVerticle`的实例被注册为地址`WORLD`上的消费者。`HelloVerticle`的实例发送消息到这个地址。如果我们部署多个`WordVerticles`，它们将依次接收消息。

可以用多种不同的形式发送消息，包括字符串、布尔值、JSON 对象和 JSON 数组。Vert.x 尽力传递，这意味着消息可能会丢失，但绝不会被故意丢弃。

```
 1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40

package nl.kabisa.vertx;

import io.vertx.core.AbstractVerticle;
import io.vertx.core.Vertx;
import io.vertx.core.http.HttpServerOptions;

public class Application {

    private static class HelloVerticle extends AbstractVerticle {

        @Override
        public void start() {
            var options = new HttpServerOptions().setPort(8080);
            vertx.createHttpServer(options)
                    .requestHandler(request ->
                            vertx.eventBus().send("WORLD", "Hello", ar -> {
                                if (ar.succeeded()) {
                                    request.response().end((String) ar.result().body());
                                } else {
                                    request.response().setStatusCode(500).end(ar.cause().getMessage());
                                }
                            }))
                    .listen();
        }
    }

    private static class WorldVerticle extends AbstractVerticle {

        @Override
        public void start() {
            vertx.eventBus().consumer("WORLD", event -> event.reply(event.body() + " world"));
        }
    }

    public static void main(String[] args) {
        var vertx = Vertx.vertx();
        vertx.deployVerticle(new WorldVerticle());
        vertx.deployVerticle(new HelloVerticle());
    }
} 
```

该示例显示消息的发送者可以指定一个可选的回复处理程序。回复以异步结果的形式提供给处理程序，可以是成功的，也可以是失败的。如果成功，则实际的回复消息可用(`ar.result()`，如示例所示)。否则，会有一个 throwable 来指示哪里出错了(`ar.cause()`，也显示在示例中)。

我可能不需要告诉你这涵盖了反应宣言的*消息驱动*部分。显然，垂直可以通过异步消息传递进行通信。

在某种程度上，这个例子也说明了*的弹性*。如果我们部署多个`WorldVerticles`,其中一个会失败，其他的会在自己的线程上继续工作。此外，这个例子显示了 Vert.x 如何提醒您在实现处理程序时考虑如何优雅地处理失败。许多处理程序以异步结果的形式接收它们的输入，正如上面所讨论的，它总是可以成功或失败。最后，也许有些矛盾，因为通过事件总线尽最大努力传递消息，您还被迫有意识地处理与丢失消息相关的失败。如果总是处理给定类型的消息非常重要，那么您需要实现确认和重试。

## 弹性

如上所述，Vert.x 为每个可用的 CPU 内核创建了两个线程，以部署如上所示的垂直系统。如果您需要处理更多的事件(例如 HTTP 请求)，您可以在具有更多 CPU 内核的机器上运行您的应用程序，并获得更多并发性的好处，而无需任何额外的编程或配置更改。此外，通过简单地部署更多或更少的某种类型的垂直组件，可以扩展应用程序的各个组件。对我来说这听起来很有弹性。

## 我们走极端吧🚢

如果你有基于回调的异步编程的经验，你可能也听说过回调地狱。回调地狱是用来描述程序缓慢但稳定地移动到屏幕右侧的术语，在那里你处理回调内部的回调，内部的回调，内部的回调，等等。

以下面的 TCP 客户端为例:

```
 1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79

package nl.kabisa.vertx.tcp;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import com.google.common.primitives.Bytes;

import io.vertx.core.AbstractVerticle;
import io.vertx.core.buffer.Buffer;
import io.vertx.core.eventbus.EventBus;
import io.vertx.core.eventbus.Message;
import io.vertx.core.json.JsonObject;
import io.vertx.core.net.NetClient;

public class TcpClientVerticle extends AbstractVerticle {

    public static final String REQUEST_ADDRESS = "tcp.client.request";

    private static final Logger LOGGER = LogManager.getLogger();

    private EventBus eventBus;
    private NetClient authClient;
    private NetClient echoClient;

    private void handleEvent(Message<JsonObject> event) {
        authClient.connect(3001, "localhost", asyncAuthSocket -> {
            if (asyncAuthSocket.succeeded()) {
                var authSocket = asyncAuthSocket.result();
                authSocket.handler(authBuffer -> {
                    if (authBuffer.getByte(0) == 0) {
                        event.fail(0, "Invalid credentials");
                    } else if (authBuffer.getByte(0) == 2) {
                        event.fail(0, "Unexpected error");
                    } else if (authBuffer.getByte(0) == 1) {
                        var id = authBuffer.getBytes(1, authBuffer.length());

                        echoClient.connect(3002, "localhost", asyncEchoSocket -> {
                            if (asyncEchoSocket.succeeded()) {
                                var echoSocket = asyncEchoSocket.result();
                                echoSocket.handler(echoBuffer -> {
                                    if (echoBuffer.getByte(0) == 0) {
                                        event.fail(500, "Unauthenticated");
                                    } else if (echoBuffer.getByte(0) == 1) {
                                        event.reply(echoBuffer.getBuffer(1, echoBuffer.length()));
                                    } else {
                                        event.fail(500, "Unexpected response from echo service");
                                    }
                                });
                                echoSocket.write(Buffer.buffer(Bytes.concat(id, event.body().getString("body").getBytes())));
                            } else {
                                String errorMessage = "Unable to obtain socket for echo service";
                                LOGGER.error(errorMessage, asyncEchoSocket.cause());
                                event.fail(500, errorMessage);
                            }
                        });
                    } else {
                        event.fail(500, "Unexpected response from authentication service");
                    }
                });
                authSocket.write(Buffer.buffer(new byte[] { 1, 2, 3, 4 }));
            } else {
                String errorMessage = "Unable to obtain socket for authentication service";
                LOGGER.error(errorMessage, asyncAuthSocket.cause());
                event.fail(500, errorMessage);
            }
        });
    }

    @Override
    public void start() {
        LOGGER.info("Starting");

        eventBus = vertx.eventBus();
        authClient = vertx.createNetClient();
        echoClient = vertx.createNetClient();

        eventBus.consumer(REQUEST_ADDRESS, this::handleEvent);
    }
} 
```

这个 verticle 监听地址`tcp.client.request`上的消息。每次消息到达时，verticle 通过交换一些字节，用一些监听端口 3001 的服务来验证自己。它使用收到的令牌与侦听端口 3002 的其他服务进行通信。最后，它用一个缓冲区来回复初始消息，该缓冲区包含从侦听端口 3002 的服务接收到的字节数组。你可能会说这不是有史以来最漂亮的代码，尽管情人眼里出西施。

(如果你想看这个应用程序其余部分基于回调的实现，由我的客人:[https://github . com/ljpengelen/vertx-demo/tree/971 e 33 e 4475 a 18 FB 7239d 716 A8 c 6d 05369442 b 8 a](https://github.com/ljpengelen/vertx-demo/tree/971e33e4475a18fb7239d716a8c6d05369442b8a)。)

## 期货

JavaScript 对回调地狱的回答是*承诺*。vert . x 对回调地狱的回答是*期货*。未来代表了在以后某个阶段潜在可用的一些计算的结果。未来要么成功，要么失败。当它成功时，其结果将是可用。当失败时，表示失败原因的 throwable 将是可用的。您可以为未来设置一个处理程序，当未来成功或失败时，将使用异步结果调用该处理程序。将期货组合成单一期货有不同的方式，我们将用一个例子来说明。

假设您想要部署许多垂直领域，并且只有在成功部署了其他垂直领域之后，才应该部署其中的一些垂直领域。Vert.x 提供了一个带有回调的 deploy 方法，当部署完成时会调用这个方法。如果不使用期货，您可能会得到这样的代码:

```
 1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34

package nl.kabisa.vertx;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import io.vertx.core.Vertx;
import nl.kabisa.vertx.http.HttpServerVerticle;
import nl.kabisa.vertx.tcp.*;

public class Application {

    private static final Logger LOGGER = LogManager.getLogger();

    private static Vertx vertx;

    public static void main(String[] args) {
        vertx = Vertx.vertx();

        vertx.deployVerticle(new AuthServiceVerticle(), authServiceDeployment -> {
            if (authServiceDeployment.succeeded()) {
                vertx.deployVerticle(new ScreamingEchoServiceVerticle(), screamingEchoServiceDeployment -> {
                    if (screamingEchoServiceDeployment.succeeded()) {
                        vertx.deployVerticle(new TcpClientVerticle(), tcpClientDeployment -> {
                            if (tcpClientDeployment.succeeded()) {
                                vertx.deployVerticle(new HttpServerVerticle(), httpServerDeployment ->
                                    LOGGER.info("All verticles started successfully"));
                            }
                        });
                    }
                });
            }
        });
    }
} 
```

这一点也不好看，即使没有额外的代码来处理可能的失败。此外，我们一次部署一个垂直项目，而实际上我们希望在其他项目部署成功后再部署`HttpServerVerticle`。

使用 futures 重写这个例子，结果如下:

```
 1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41

package nl.kabisa.vertx;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import io.vertx.core.*;
import nl.kabisa.vertx.http.HttpServerVerticle;
import nl.kabisa.vertx.tcp.*;

public class Application {

    private static final Logger LOGGER = LogManager.getLogger();

    private static Vertx vertx;

    private static Future<String> deploy(Vertx vertx, Verticle verticle) {
        Future<String> future = Future.future();
        vertx.deployVerticle(verticle, future);
        return future;
    }

    public static void main(String[] args) {
        LOGGER.info("Starting");

        vertx = Vertx.vertx();

        CompositeFuture.all(
                deploy(vertx, new AuthServiceVerticle()),
                deploy(vertx, new ScreamingEchoServiceVerticle()),
                deploy(vertx, new TcpClientVerticle()))
                .compose(s -> deploy(vertx, new HttpServerVerticle()))
                .setHandler(s -> {
                            if (s.succeeded()) {
                                LOGGER.info("All verticles started successfully");
                            } else {
                                LOGGER.error("Failed to deploy all verticles", s.cause());
                            }
                        }
                );
    }
} 
```

这里，我们同时部署三个垂直市场，并在所有其他垂直市场部署成功后部署最后一个。还是那句话，情人眼里出西施，但这对我已经足够好了。

你还记得上面看到的 TCP 客户端吗？下面是使用 futures 实现的同一个客户端:

```
 1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102
103
104

package nl.kabisa.vertx.tcp;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

import com.google.common.primitives.Bytes;

import io.vertx.core.AbstractVerticle;
import io.vertx.core.Future;
import io.vertx.core.buffer.Buffer;
import io.vertx.core.eventbus.EventBus;
import io.vertx.core.eventbus.Message;
import io.vertx.core.json.JsonObject;
import io.vertx.core.net.NetClient;
import io.vertx.core.net.NetSocket;

public class TcpClientVerticle extends AbstractVerticle {

    public static final String REQUEST_ADDRESS = "tcp.client.request";

    private static final Logger LOGGER = LogManager.getLogger();

    private EventBus eventBus;
    private NetClient authClient;
    private NetClient echoClient;

    private Future<NetSocket> connectToAuthService() {
        Future<NetSocket> future = Future.future();

        authClient.connect(3001, "localhost", future);

        return future;
    }

    private Future<Buffer> authenticate(NetSocket authSocket) {
        Future<Buffer> future = Future.future();

        authSocket.handler(authBuffer -> {
            if (authBuffer.getByte(0) == 0) {
                future.fail("Invalid credentials");
            } else if (authBuffer.getByte(0) == 2) {
                future.fail("Unexpected error");
            } else if (authBuffer.getByte(0) == 1) {
                future.complete(authBuffer.getBuffer(1, authBuffer.length()));
            } else {
                future.fail("Unexpected response from authentication service");
            }
        });

        authSocket.write(Buffer.buffer(new byte[] { 1, 2, 3, 4 }));

        return future;
    }

    private Future<NetSocket> connectToEchoClient() {
        Future<NetSocket> future = Future.future();

        echoClient.connect(3002, "localhost", future);

        return future;
    }

    private Future<Buffer> forwardToEchoClient(NetSocket echoSocket, Buffer token, String input) {
        Future<Buffer> future = Future.future();

        echoSocket.handler(echoBuffer -> {
            if (echoBuffer.getByte(0) == 0) {
                future.fail("Unauthenticated");
            } else if (echoBuffer.getByte(0) == 1) {
                future.complete(echoBuffer.getBuffer(1, echoBuffer.length()));
            } else {
                future.fail("Unexpected response from echo service");
            }
        });
        echoSocket.write(Buffer.buffer(Bytes.concat(token.getBytes(), input.getBytes())));

        return future;
    }

    private void handleEvent(Message<JsonObject> event) {
        connectToAuthService()
                .compose(this::authenticate)
                .compose(token -> connectToEchoClient()
                        .compose(socket -> forwardToEchoClient(socket, token, event.body().getString("body"))))
                .setHandler(asyncBuffer -> {
                    if (asyncBuffer.succeeded()) {
                        event.reply(asyncBuffer.result());
                    } else {
                        event.fail(500, asyncBuffer.cause().getMessage());
                    }
                });
    }

    @Override
    public void start() {
        LOGGER.info("Starting");

        eventBus = vertx.eventBus();
        authClient = vertx.createNetClient();
        echoClient = vertx.createNetClient();

        eventBus.consumer(REQUEST_ADDRESS, this::handleEvent);
    }
} 
```

尽管我仍然需要仔细观察`handleEvent`方法到底在做什么，但我希望我们能同意这是对基于回调的实现的改进。在我看来，实现的每个部分负责什么，哪些部分是相关的，这样更清晰。

## 结论

通过查看这几个例子，您已经看到了 Vert.x 提供的部分功能。然而，它并没有结束于这里所描述的。 [Vert.x 的文档页面](https://vertx.io/docs/)提供了一个包含完整工具包的书籍、手册和 API 文档的综合列表。还有一页列出了[学习材料](http://vertx.io/materials/)，应该可以帮助你开始学习。

如果你对这个工具包感兴趣，你绝对应该尝试一下在[https://github.com/ljpengelen/vertx-demo/](https://github.com/ljpengelen/vertx-demo/)的示例应用程序。除了这里描述的几个其他垂直领域之外，还有一些测试应该会让你对 Vert.x 提供的东西有一个印象。

一旦你掌握了窍门，用 Vert.x 开发应用程序是相当令人愉快的。然而，与所有形式的异步编程一样，我有时会发现自己处于一种有点恼人的情况，在这种情况下，同步方法更容易实现和推理。问题是你是否愿意忍受一点额外的工作来享受反应式系统的潜在好处。
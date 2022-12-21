# 熟悉的第四天——去某个地方

> 原文：<https://dev.to/nickblow/familiar-day-4-getting-somewhere-baa>

## 如果你是新来的

我目前正在用最新的技术构建一个开源的桌面角色扮演助手。我从基础设施和构建工具开始，以确保我有一个坚实的基础。我已经设置了一个不错的入门 Terraform 配置，并决定将我的 JavaScript 编译从 ParcelJS 转移到 WebPack，因为我最终不得不在 ParcelJS 上设置一大堆配置，这挫败了“无配置”库的观点。我上次说过我会开发 WebPack，但我最终还是开发了 GraphQL 订阅服务，因为我也需要它来做一些我正在专业从事的工作...

### 和昨天的一个小修正

我提到过，缺少对 Lit-Element 的服务器端渲染没什么大不了的，因为 Lit-Element 很快。然而，今天早上我意识到这个*可能会导致搜索引擎优化的问题，因为即使必应和谷歌声称索引 Javascript，这是相当零星和不可靠的。我会考虑如何处理这个问题，最好能有一个公开的竞选日志，让人们跟着做，这将是一个良好的开端。*

正如我昨天提到的，没有什么可以阻止我们将技术与 Lit-Element 混合和匹配。所以我想我会用 Lit-HTML 创建大部分的活动日志，使用网络组件来逐步增强互动元素。这听起来像是一个富有成效的探索途径。唯一的问题是，我需要小心不在 ShadowDOM 中的 CSS 作用域和命名。再水化仍然是一个公开的问题，但是我也许可以把 Lit-HTML 内容当作不透明的 HTML 来处理，并解决它——我甚至可能只在服务器上使用 Lit-HTML，但是需要想出该怎么做。我昨天没有提到的一个选项是利用浏览器将未知的自定义标签视为 [HTMLUnknownElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLUnknownElement) 的事实，做一些类似于:
的事情

```
<my-tag>
  <span slot="ssr">hello world</span>
</my-tag> 
```

这将呈现“hello world ”,即使页面上没有任何 JS，以一种符合标准的方式。还有一个关于[声明式阴影世界](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Declarative-Shadow-DOM.md)的提议将永久解决这个问题。

#### 项目结构快速说明

我使用了一种 monorepo 方法，但是每个文件夹都被视为完全独立的服务。它不会有几百万行代码(谁知道呢，但可能不会)。除了客户端 JS 和服务于 JS & HTML 的服务器，我不打算在模块之间使用代码共享，因为这使得同构应用最容易。如果我想有一个两者共享的模块，我不会在 Go 中做`import myFile from '../../../../../../otherFolder/file.ts`或等效的操作。我将把它作为一个完全独立的存储库发布到 Git 上(尽管如此，有了新的 [Go 模块](https://github.com/golang/go/wiki/Modules)，我也许能够做一些新奇的事情，仍然保持服务之间的隔离。

##### Webpack &我们的项目

我们可能需要多个 webpack 配置，一个用于服务器，一个用于 web。我还保存了一个包含项目信息的顶层 package.json，但会将所有前端配置放在前端文件夹中。

#### 我是如何接近 Git 的

现在我把一切都推给了师父。我的理念是 master 应该总是可部署的，并且代表系统的当前状态。如果我正在构建一个突破性的变化或者新的特性，我会在一个分支上构建它，并且只有当我乐意在现场部署它的时候才合并它。开始时很困难，因为我们没有任何东西可以建造。我现在可能会开始在一个新的分支上构建前端，一旦我很高兴代码可以在 web 上运行，就把它合并进来。

### 我今天在做什么

我将在工作中使用 GraphQL 订阅，所以我决定在这方面努力，而不是摆弄 WebPack。我更新了我在 AWS 上做的一些东西，使其更具可插拔性。最终我会抽出时间让端到端的例子在 AWS 和 GCP 上运行。

下面是我取得的进展[https://github . com/nick blow/gqlssehandlers/commit/ab 1333 EB 6 fffb 92 f 8522 CBE 73 ef 90952 A8 C1 b 932](https://github.com/NickBlow/gqlssehandlers/commit/ab1333eb6fffb92f8522cbe73ef90952a8c1b932)

它在本地工作，`go run examples/example-server.go`然后就`curl 0.0.0.0:8080`看它工作。现在，它只是向所有客户端组播，每隔几秒发送一条“Hello”消息，如果 3 秒内没有收到任何消息，则保持活动状态。

我希望这有点可插拔性，但不幸的是，看起来确实有很多工作要为配置编写接口——您需要处理存储订阅、填充 graphql 参数以及监听事件流。然而，这应该使它具有合理的灵活性。

下面是一个基本的服务器设置的样子:

```
package main

import (
    "log"
    "net/http"
    "time"

    "github.com/NickBlow/gqlssehandlers"
    "github.com/NickBlow/gqlssehandlers/examples/adapters"

    gorrilaHandlers "github.com/gorilla/handlers"
    "github.com/gorilla/mux"
)

func main() {

    router := mux.NewRouter()
    eventStream := &adapters.InMemoryAdapter{}

    subscriptionServerConfig := &gqlssehandlers.HandlerConfig{
        Adapter:         eventStream,
        EventBufferSize: 100,
    }

    handlers := gqlssehandlers.GetHandlers(subscriptionServerConfig)
    router.Handle("/", handlers.PublishStreamHandler).Methods("GET")
    router.Handle("/subscriptions", handlers.SubscribeHandler).Methods("POST")
    router.Handle("/subscriptions", handlers.UnsubscribeHandler).Methods("DELETE")

    originsOk := gorrilaHandlers.AllowedOrigins([]string{"https://example.com"})
    methodsOk := gorrilaHandlers.AllowedMethods([]string{"GET", "POST", "OPTIONS", "DELETE"})

    // Server has long write timeout because we're supporting a streaming response.
    srv := http.Server{
        Addr:         ":8080",
        Handler:      gorrilaHandlers.CORS(originsOk, methodsOk)(router),
        ReadTimeout:  15 * time.Second,
        WriteTimeout: 15 * time.Minute,
    }

    log.Printf("Server starting on port 8080")
    log.Fatal(srv.ListenAndServe())

} 
```

现在，该库返回一个订阅处理程序、一个取消订阅处理程序和一个流处理程序。因为服务器发送的事件只作用于 GET 请求，所以我需要想出一些方法将身份验证添加到流处理程序中。这两个选项是在握手时设置 cookie，或者在查询字符串中创建某种短期的一次性密码。理想情况下，我不想做任何类似验证 JWT 的事情，但也许我们可以让它可配置。

我肯定需要在库的开发者体验(DX)上努力。现在，配置看起来像这样:

```
// HandlerConfig represesents the configuration options for GQLSSEHandlers
// If the EventBuffer size has been set, the server will store events in memory,
// and re-send them if a client reconnects with a Last-Event-ID Header. This may result in duplicate messages being sent,
// so ensure your client is idempotent.
// This will also only send events that the server itself received.
// If you want to ensure a client always gets buffered messages, you can either use sticky sessions, route based on some hash, or multicast events to all servers.
type HandlerConfig struct {
    Adapter         SubscriptionAdapter
    EventBufferSize int64
} 
```

订阅适配器看起来像:

```
 // SubscriptionAdapter represents an interface that begins listening to a stream of arbitrary events (e.g. a queue or a pub/sub service),
// and calls the callback with an array of interested clients whenever it receives an event.
type SubscriptionAdapter interface {
    StartListening(cb orchestration.NewEventCallback)
    NotifyNewSubscription(subscriber orchestration.SubscriptionData) error
    NotifyUnsubscribe(subscriptionID string) error
} 
```

在这个适配器中隐藏了许多复杂性，例如在我正在工作但尚未发布源代码的 AWS 示例中，StartListening 事件使用 SQS + SNS，notify news subscription/notify unsubscription 方法使用 DynamoDB，这是一个适配器中塞进的许多服务。我也不相信编排包的名字。

SubscriptionData 也非常超载，因为它包含了执行 GraphQL 查询所需的所有信息。一方面，这将很多责任转移给了库的集成者，然而另一方面，这意味着你可以做一些事情，比如将一个[数据加载器](https://github.com/graph-gophers/dataloader)粘贴到上下文中，并且对执行什么有更多的控制。

```
// SubscriptionData encompasses a particular subscription and the parameters for the GraphQL Query that should be performed.
type SubscriptionData struct {
    ID            string
    GraphQLParams *graphql.Params
} 
```

### 一个示例适配器

我仍然需要为此创建一个模拟的 graphQL 模式——目前，流服务器只是将订阅 ID 多播给每个人。

```
package adapters

import (
    "time"

    "github.com/NickBlow/gqlssehandlers/internal/orchestration"
)

type subscriptionData = orchestration.SubscriptionData

var subscribersMap = make(map[string]subscriptionData)

// InMemoryAdapter stores subscribers in memory, and triggers events at random intervals
type InMemoryAdapter struct{}

//StartListening calls the callback at random intervals
func (a *InMemoryAdapter) StartListening(cb orchestration.NewEventCallback) {

    go func() {
        for {
            <-time.After(time.Second * 10)
            cb([]subscriptionData{subscriptionData{ID: "Hello"}})
        }
    }()

}

// NotifyNewSubscription adds a subscriber to the map
func (a *InMemoryAdapter) NotifyNewSubscription(subscriber subscriptionData) error {
    subscribersMap[subscriber.ID] = subscriber
    return nil
}

// NotifyUnsubscribe removes a subscriber from the map
func (a *InMemoryAdapter) NotifyUnsubscribe(subscriber string) error {
    delete(subscribersMap, subscriber)
    return nil
} 
```

### 工作原理:

服务器发送事件本身非常简单:你需要一个实现 [Flusher](https://golang.org/pkg/net/http/#Flusher) 接口的编写器。[这篇](https://thoughtbot.com/blog/writing-a-server-sent-events-server-in-go)博文帮助我找到了正确的方向。然后你需要设置几个标题:

```
Cache-Control: no-cache
Connection: keep-alive
Content-Type: text/event-stream
Transfer-Encoding: chunked 
```

确保你的内容适当地控制新行[看这里](https://www.html5rocks.com/en/tutorials/eventsource/basics/)和[这里](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events#Event_stream_format)。

这是我的流处理器的完整代码:

```
func (s *Handler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    flusher, ok := w.(http.Flusher)
    if !ok {
        http.Error(w, "Streaming unsupported!", http.StatusInternalServerError)
        return
    }
    closed := w.(http.CloseNotifier).CloseNotify()
    clientID, err := gonanoid.Nanoid()
    if err != nil {
        fmt.Println("Couldn't generate client ID")
        http.Error(w, "Error", http.StatusInternalServerError)
        return
    }
    w.Header().Set("Content-Type", "text/event-stream")
    w.Header().Set("Cache-Control", "no-cache")
    w.Header().Set("Connection", "keep-alive")
    messageChan := make(chan string)

    s.Broker.NewClients <- orchestration.ClientInfo{
        ClientID:             clientID,
        CommunicationChannel: messageChan,
    }

Loop:
    for {
        select {
        case <-closed:
            s.Broker.ClosedClients <- clientID
            break Loop
        case <-time.After(time.Second * 3):
            fmt.Fprint(w, ":KEEPALIVE \n\n")
            flusher.Flush()
        case val := <-messageChan:
            fmt.Fprintf(w, "data:%v \n\n", val)
            flusher.Flush()
        }
    }
    fmt.Println("stopped main thread")
} 
```

以防消息不足，服务器每 3 秒发送一次 keepalive，不过我稍后会增加这个值。示例服务器有一个超时，这是[好习惯](https://blog.cloudflare.com/the-complete-guide-to-golang-net-http-timeouts/)，但是因为我们正在发送一个流响应，所以我把它设置为 15 分钟。[事件源](https://developer.mozilla.org/en-US/docs/Web/API/EventSource)自动重新连接，我们也希望能够清空旧服务器上的连接，这样当我们更新软件时，就不会有永远连接的客户端，并且能够自动清理表现不佳和丢失的客户端。

引用的`Broker`负责跟踪连接的客户端，并从上面的`NewEventCallback`向它们发送消息。这些消息将记录在客户端的 messageChan 中。

这是经纪人的样子。

```
// ClientInfo contains information about a connected client
type ClientInfo struct {
    ClientID             string
    CommunicationChannel chan string
    LastSeenEventID      string
    subscriptions        []string
}

var subscriptionLookupTable = make(map[string]ClientInfo)

// Broker contains all the details to manage state of connected clients. 
type Broker struct {
    NewClients           chan ClientInfo
    ClosedClients        chan string
    NewSubscriptions     chan SubscriptionData
    executeSubscriptions chan SubscriptionData
    bufferedEvents       []interface{}
    clients              map[string]ClientInfo
}

// InitializeBroker creates a broker and starts listening to events
func InitializeBroker() *Broker {
    b := &Broker{
        NewClients:           make(chan ClientInfo),
        ClosedClients:        make(chan string),
        NewSubscriptions:     make(chan SubscriptionData),
        executeSubscriptions: make(chan SubscriptionData),
        bufferedEvents:       make([]interface{}, 0),
        clients:              map[string]ClientInfo{},
    }
    go b.listen()
    return b
}

// NewEventCallback is a function that should be called every time an event happens,
// and contain details of the subscriptions that should be called in response to that event
type NewEventCallback func(subscriptions []SubscriptionData)

// ExecuteQueriesAndPublish triggers the broker to perform the GraphQL Query specified in the SubscriptionData, and propagate it to the clients
func (b *Broker) ExecuteQueriesAndPublish(subscriptions []SubscriptionData) {
    for _, val := range subscriptions {
        b.executeSubscriptions <- val

    }
}

func (b *Broker) listen() {
    for {
        select {
        case client := <-b.NewClients:
            b.clients[client.ClientID] = client
        case client := <-b.ClosedClients:
            delete(b.clients, client)
        case event := <-b.executeSubscriptions:
            for _, client := range b.clients {
                client.CommunicationChannel <- event.ID
            }
        }
    }
} 
```

显然，它仍然是一个正在进行中的工作，因为 ExecuteQueriesAndPublish 并没有做到它在锡说什么！

#### 好的

尽管这是一个“GraphQL”服务器，但我还没有对订阅做任何事情，或者创建一个示例模式...我会说的。我可能还想为此做一个 [Apollo Link](https://github.com/apollographql/apollo-link) 集成。

我还有很多事情要做，但我们今天取得了很大进展。让我们看看周末过得怎么样！我正在尽可能长时间地每天做一点...
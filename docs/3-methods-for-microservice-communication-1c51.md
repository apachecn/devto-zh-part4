# 微服务通信的 3 种方法

> 原文：<https://dev.to/bnevilleoneill/3-methods-for-microservice-communication-1c51>

**作者:[凯尔·加尔布雷斯](https://blog.logrocket.com/author/kylegalbraith/)T3】✏️**

在微服务架构的世界里，我们通过一系列服务来构建应用。集合中的每个服务都符合以下标准:

*   松散耦合
*   可维护和可测试
*   可以独立部署

微服务架构中的每个服务解决应用程序中的一个业务问题，或者至少支持一个业务问题。单个团队负责应用程序中的一个或多个服务。

微服务架构可以带来许多不同的好处。

*   它们通常更容易构建和维护
*   服务是围绕业务问题组织的
*   它们提高了生产率和速度
*   他们鼓励自主、独立的团队

这些好处是微服务越来越受欢迎的一个重要原因。但是坑洞的存在会破坏所有这些好处。点击这些，你会得到一个架构，只不过是分布式技术债务。

微服务之间的通信就是这样一个漏洞，如果不提前考虑，可能会造成严重破坏。

这种架构的目标是创建松散耦合的服务，而通信在实现这一目标的过程中起着关键作用。在本文中，我们将关注微服务架构中服务通信的三种方式。正如我们将要看到的，每一种都有自己的优点和缺点。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## HTTP 通信

在选择服务如何相互通信时，直接的领导者往往是 HTTP。事实上，我们可以假设**所有的**通信信道都源自这个信道。但是，除此之外，服务之间的 HTTP 调用是服务到服务通信的可行选择。

如果我们的架构中有两个服务，它可能看起来像这样。`ServiceA`可能会处理一个请求，并调用`ServiceB`来获取另一条信息。

```
function process(name: string): Promise<boolean> {
    /** do some ServiceA business logic
        ....
        ....
    */
    /**
     * call ServiceB to run some different business logic
    */
    return fetch('https://service-b.com/api/endpoint')
        .then((response) => {
            if (!response.ok) {
                throw new Error(response.statusText)
            } else {
                return response.json().then(({saved}) => {
                    return saved
                })
            }
        })
} 
```

Enter fullscreen mode Exit fullscreen mode

代码是自解释的，适合微服务架构。`ServiceA`拥有一块业务逻辑。它运行自己的代码，然后调用`ServiceB`来运行另一个业务逻辑。在这段代码中，第一个服务是*等待*第二个服务在返回之前完成*。*

这里我们有两个服务之间的同步 HTTP 调用。这是一种可行的通信模式，但是它确实在两个服务之间产生了我们可能不需要的耦合。

HTTP 领域的另一个选择是两个服务之间的异步 HTTP。这可能是这样的:

```
function asyncProcess(name: string): Promise<string> {
    /** do some ServiceA business logic
        ....
        ....
    */
    /**
     * call ServiceB to run some different business logic
    */
    return fetch('https://service-b.com/api/endpoint')
        .then((response) => {
            if (!response.ok) {
                throw new Error(response.statusText)
            } else {
                return response.json().then(({statusUrl}) => {
                    return statusUrl
                })
            }
        })
} 
```

Enter fullscreen mode Exit fullscreen mode

变化是微妙的。现在，`ServiceB`不是返回一个`saved`属性，而是返回一个`statusUrl`。这意味着这个服务现在接受来自第一个服务的请求，并且**立即**返回一个 URL。这个 URL 可以用来检查请求的进度。

我们已经将两个服务之间的通信从同步转换为异步。现在，第一个服务不再停留在等待第二个服务完成后再返回。

使用这种方法，我们保持服务相互隔离，并且耦合是松散的。

缺点是它在第二个服务上创建了额外的 HTTP 请求；现在将从外部轮询它，直到请求完成。这也给客户端带来了复杂性，因为它现在必须检查请求的进度。

但是，异步通信允许服务之间保持松散耦合。

## 消息交流

我们可以在微服务架构中利用的另一种通信模式是基于消息的通信。

与 HTTP 通信不同，所涉及的服务并不直接相互通信。相反，服务将消息推送到其他服务订阅的消息代理。这消除了许多与 HTTP 通信相关的复杂性。

它不需要服务知道如何相互交谈；它消除了服务之间直接调用的需要。相反，所有服务都知道一个消息代理，并且它们将消息推送给该代理。其他服务可以选择订阅它们关心的代理中的消息。

如果我们的应用程序在 Amazon Web Services 中，我们可以使用简单通知服务(SNS)作为我们的消息代理。现在,`ServiceA`可以向一个 SNS 主题推送消息,`ServiceB`可以收听该主题。

```
function asyncProcessMessage(name: string): Promise<string> {
    /** do some ServiceA business logic
        ....
        ....
    */
    /**
     * send message to SNS that ServiceB is listening on
    */
    let snsClient = new AWS.SNS()
    let params = {
        Message: JSON.stringify({
            'data': 'our message data'
        }),
        TopicArn: 'our-sns-topic-message-broker'
    }

    return snsClient.publish(params)
        .then((response) => {
            return response.MessageId
        })
} 
```

Enter fullscreen mode Exit fullscreen mode

`ServiceB`收听社交网络话题上的消息。当它收到一个它关心的消息时，它就执行它的业务逻辑。

这带来了自身的复杂性。请注意，`ServiceA`不再接收状态 URL 来检查进度。这是因为我们只知道消息已经发出，而不知道`ServiceB`已经收到。

这可以用许多不同的方法来解决。一种方法是将`MessageId`返回给调用者。它可以用它来查询`ServiceB`，T1 将存储它收到的消息的`MessageId`。

请注意，使用这种模式的两个服务之间仍然存在一些耦合。例如，`ServiceB`和`ServiceA`必须就消息的结构和内容达成一致。

## 事件驱动的通信

在本文中，我们将探讨的最后一种通信模式是事件驱动模式。这是另一种异步方法，它试图完全消除服务之间的耦合。

与服务必须知道公共消息结构的消息传递模式不同，事件驱动的方法不需要这一点。服务之间的通信通过单个服务产生的事件进行。

这里仍然需要一个消息代理，因为单个服务会将它们的事件写入其中。但是，与消息方法不同，消费服务不需要知道事件的细节；他们对事件的发生做出反应，而不是对事件可能传递或可能不传递的信息做出反应。

在正式术语中，这通常被称为“仅事件驱动的通信”我们的代码就像我们的消息传递方法，但是我们推送到 SNS 的事件是通用的。

```
function asyncProcessEvent(name: string): Promise<string> {
    /** do some ServiceA business logic
        ....
        ....
    */
    /**
     * call ServiceB to run some different business logic
    */
    let snsClient = new AWS.SNS()
    let params = {
        Message: JSON.stringify({
            'event': 'service-a-event'
        }),
        TopicArn: 'our-sns-topic-message-broker'
    }

    return snsClient.publish(params)
        .then((response) => {
            return response.MessageId
        })
} 
```

Enter fullscreen mode Exit fullscreen mode

注意这里我们的 SNS 主题消息是一个简单的`event`属性。每个服务都同意以这种格式将事件推送到代理，这保持了通信的松散耦合。服务可以监听它们关心的事件，并且它们知道运行什么逻辑来响应它们。

这种模式使服务保持松散耦合，因为事件中不包含任何有效负载。这种方法中的每个服务对事件的发生做出反应，以运行其业务逻辑。这里，我们通过 SNS 主题发送事件。也可以使用其他事件，比如文件上传或数据库行更新。

## 结论

在基于微服务的架构中，这些都是可能的通信模式吗？肯定不是。服务有更多的方式以同步和异步模式进行通信。

但是，这三个突出了支持同步和异步的优点和缺点。在选择其中一个时，需要考虑耦合因素，但是也需要考虑开发和调试因素。

如果你对这篇博文、AWS、无服务器或一般编码有任何问题，请随时通过 twitter [@kylegalbraith](https://twitter.com/kylegalbraith) 联系我。也可以看看我每周的[边做边学简讯](https://kylegalbraith.com/learn-by-doing)或者我的[通过使用 It 学习 AWS](https://kylegalbraith.com/learn-aws)课程，了解更多关于云、编码和 DevOps 的知识。

* * *

**编者按:**看到这个帖子有问题？你可以在这里找到正确的版本。

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[微服务沟通的 3 种方法](https://blog.logrocket.com/methods-for-microservice-communication/)最早出现在 [LogRocket 博客](https://blog.logrocket.com)上。
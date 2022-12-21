# SSE(服务器发送的事件)与 WebSocket

> 原文：<https://dev.to/rajdeepdas/sseserver-sent-events-vs-websocket-1lnl>

<figure>[![](img/6978dfc07038f5a3cb96c4d88a6f4a31.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_vJas3Wz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/880/1%2AkFmD_qjKpsg8BqftU0kqtQ.jpeg)

<figcaption></figcaption>

</figure>

对于初学者来说，WebSockets 和 SSEs 都使用 HTTP 连接，尽管在 WebSockets 的情况下，TCP 握手有效地从 HTTP 协议升级到 WebSocket 协议，允许 WebSocket 应用程序更容易地适应现有的基础设施。然而，两者之间的主要相似之处在于它们的功能:都将数据从客户端推送到服务器，这个过程也称为“服务器推送”。考虑到这一点，在讨论这两种技术的最佳用例之前，让我们先来看看两者之间的一些差异。

在我们讨论这两种技术之间的区别之前，我们应该声明它们不是竞争技术，也没有谁比谁更好。WebSockets 比 SSEs 受欢迎的原因可以解释为 WebSockets 比 SSEs 受到了更多的关注(和欣赏),事实上更多的浏览器支持它而不是支持 SSEs。

这两种技术之间的差异并不是很大，如果您只想做**“服务器推送”**，这两种技术都是不错的选择。

> 到目前为止，这两种技术的最大区别在于 WebSockets 是客户端和服务器之间的全双工双向通信，而 SSEs 是单向的。

SSEs 附带了一组 WebSockets 在设计上缺乏的功能，比如**自动重新连接**，事件 id 和发送任意事件。另一方面，WebSockets 能够检测到断开的客户端连接，而 SSEs 在检测到相同的问题之前首先需要发送一条消息。在浏览器支持方面，Internet Explorer 和 Edge 都还不支持 SSE，尽管模拟 SSE 功能的 polyfills 可以解决这个问题。

尽管 WebSockets 使用初始的 HTTP 连接，但是该连接在 TCP 握手之后被更新，之后数据通过 WebSocket 协议被发送。这是一个比 SSE 协议更复杂的协议。因为它们是**双向的**，WebSocktets 比 SSEs 需要更多的开发工作，只需要*发送一个带有特定报头的 HTTP 消息*，而 WebSocket 需要建立和维护一个 *TCP 套接字通信，以及服务器端的监听器套接字。*

很高兴知道 SSE 受到最大打开连接数的限制，这在打开各种标签时会特别痛苦，因为每个浏览器的限制是六个。**只有 WebSockets 可以同时传输二进制数据和 UTF-8，而 SSE 仅限于 UTF-8。**

虽然 WebSockets 比 SSEs 更复杂、要求更高，但全双工 TCP 连接使其适用于更广泛的应用场景。SSE 是一个更简单、更快速的解决方案，但它不可扩展:如果您的 web 应用程序需求发生变化，就需要使用 WebSocket 进行重构。尽管 WebSocket 技术提供了更多的前期工作，但它是一个更加通用和可扩展的框架，因此对于那些会随着时间的推移添加新功能的复杂应用程序来说，它是一个更好的选择。

### WS 和 SSE 最佳用例

最后，你应该使用 WebSockets 还是 SSEs 取决于你的用例。如前所述，与 WebSockets 相反，SSEs 不能提供双向客户端-服务器通信。需要这种通信的用例是实时多人游戏、消息和聊天应用。 **当不需要从客户端发送数据时，SSEs 可能是比 WebSockets 更好的选择。**这种用例的例子有**状态更新**、**新闻订阅**和其他自动数据推送机制。

### Server Sence(RajdeepDev 即将推出的项目)如何使用 SSE

ServerSence 是实时服务器、Web 应用程序监控解决方案，可针对服务器或 Web 应用程序故障发出即时警报。您不必为您的应用开发或维护监控解决方案，我们会处理好这一点，因此您可以为客户提供出色的用户体验。

**服务器感知**仪表板使用**感知**实时可视化服务器和 Web 应用程序状态，并发送实时警报。(如果你想成为该项目的一部分，请联系 rajdeepdas.india@gmail.com 的)。

### 结论

在这篇文章中，我们比较了 WebSockets 和 SSEs。我们讨论了这两种技术之间的差异和相似之处。虽然两者都可以用于“服务器推送”，但两者之间有许多微妙的区别。因为 WebSockets 受到了开发者和博客作者的更多关注，所以**ses 不太为人所知，但影响力丝毫不减**。

### 发布者

*最初发表于*[*【https://www.linkedin.com】*](https://www.linkedin.com/pulse/sseserver-sent-events-vs-websocket-rajdeep-das)*。*
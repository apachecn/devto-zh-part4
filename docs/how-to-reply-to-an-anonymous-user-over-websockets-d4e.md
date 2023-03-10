# 如何通过 Websockets 回复匿名用户

> 原文：<https://dev.to/alexandrum/how-to-reply-to-an-anonymous-user-over-websockets-d4e>

# 大家好！

这是我在 **dev.to** 上的第一篇帖子，我给自己一些勇气来写它，因为我一直想有一个博客或某个地方来分享我学到的东西。

所以我们开始吧。

几周前，我开始更深入地研究 Spring web socket T1 和 T2 STOMP T3，因为我有两个应用程序，一个服务器和一个客户端，它们必须以客户端请求一些数据而服务器回复 T5 的方式进行通信。

我知道这听起来很简单，但是我不得不面对的挑战是从服务器回复一个所谓的匿名用户，因为客户端应用程序本身并不是一个用户 T2。 [![Anonymous user](img/fa818c7f25e05ed5048d8dde21726e0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PhSmI5Ua--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.tenor.cimg/4a600f6af42eeffaf5f110cfcd1e87fd/tenor.gif%3Fitemid%3D4793905)

因此，在几个网站上到处寻找，我发现我可以使这个特殊的情况在某种程度上类似于当你使用主应用程序的用户时的情况。

这个想法是让*匿名用户*成为**可见的**，关键是在两个应用程序之间进行*握手*时为他创建一个特定的**会话 Id**

```
public class CustomHandshakeHandler extends DefaultHandshakeHandler {
  // Custom class for storing principal
  @Override
  protected Principal determineUser(ServerHttpRequest request,
                                    WebSocketHandler wsHandler,
                                    Map<String, Object> attributes) {
    // Generate principal with UUID as name
    return new StompPrincipal(UUID.randomUUID().toString());
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我还将主 *java.security.Principal* 类扩展成了一个 **StompPrincipal** ，以便为我的案例
定制一些东西

```
public class StompPrincipal implements Principal {

  private String name;

  public StompPrincipal(String name) {
    this.name = name;
  }

  @Override
  public String getName() {
    return name;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

差不多就是这样了，现在监听像 */tutorial-request* 这样的路径，并把**SimpMessageHeaderAccessor**的一个对象作为参数，我可以检索会话信息，**直接回复**给发出请求的*用户*:

```
 @Autowired
  private SimpMessagingTemplate messagingTemplate;

  @MessageMapping("/tutorial-request")
  public void handleRequest(SimpleRequest request, SimpMessageHeaderAccessor headerAccessor) {
    String sessionId = headerAccessor.getSessionId();
    log.info("Handling session with id {} receiving message {} from {}", sessionId, request.getText(), request.getFrom());
    SimpleResponse response = new SimpleResponse("Hello!");
    messagingTemplate.convertAndSendToUser(headerAccessor.getUser().getName(), "/queue/tutorial-request", response);
    log.info("Replied to {} with message {}", sessionId, response);
  } 
```

Enter fullscreen mode Exit fullscreen mode

我跳过了添加 *SimpleRequest* 和 *SimpleResponse* 的步骤，因为这些只是简单的对象，可以和上面的整个代码一起在存储库中找到:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [亚历山大](https://github.com/alexandrumm) / [春天-教程](https://github.com/alexandrumm/spring-tutorials)

### Spring 教程资源

请在评论中告诉我你的想法，我知道我在写帖子方面还有很多要学习和提高的地方，我希望这只是一个开始，我会感谢任何建议，所以请这样做。

编码快乐！
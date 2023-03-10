# 把你的蜜獾事件传到扎皮尔的大本营

> 原文：<https://dev.to/kylekeesling/piping-your-honeybadger-events-to-basecamp-with-zapier-20d1>

我的团队一直致力于巩固和改进我们的沟通工具和实践，作为其中的一部分，我们决定摆脱[懈怠](http://slack.com)，完全利用[大本营](http://basecamp.com)进行所有内部和客户沟通。

在 Slack 中，团队赞赏的主要事情之一是我创建的#dev 通道，它将我们所有的部署、拉请求和错误集中到一个中心位置，让每个人都知道发生了什么，而不必拍拍别人的肩膀。

Basecamp 提供了一种简单的方法，使用一个[聊天机器人](https://github.com/basecamp/bc3-api/blob/master/sections/chatbots.md)将 Github 事件加入到篝火聊天中，但是让我们的应用程序出错并不那么简单。

我们使用[蜜獾](https://www.honeybadger.io/)来跟踪我们的错误，虽然他们[提供许多集成](https://docs.honeybadger.io/guides/integrations.html)，但 Basecamp 不在其中——进入[扎皮尔](https://zapier.com)。

## 我是怎么做到的？

扎皮尔把它变成了一个非常简单的过程:

1.  创建一个[捕捉网络钩子](https://zapier.com/apps/webhook/integrations)来捕捉从蜜獾传入的[网络钩子](https://docs.honeybadger.io/guides/services.html#1-select-the-webhook-integration)
2.  创建一个 POST webhook 动作，将来自 Honeybadger 的 JSON 有效负载转换成 Basecamp 期望聊天机器人使用的格式

### 大本营的战果

[![Honeybadger Chatbot Example](img/28b8f4667136f208d2aff9b402b1d9fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jj_3d2l_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kylekeesling.dimg/2019/07/honeybadger-chatbot1.png)

使用`details`和`summary`元素可以让你在 Basecamp 中创建一个可折叠的帖子。在上面的例子中，注意黑色的右箭头，如果你点击它，你甚至可以得到一个堆栈跟踪的小例子。下面是我为聊天机器人有效负载使用的`contents`键的值:

```
<details>
      <summary>
        <strong>💥({xxx __fault__ id}){xxx__message}</strong><br>
        <a href="{xxx __fault__ url}">View in Honeybadger</a>
      </summary>
      <br><hr><br>
      <pre>{xxx __notice__ application_trace}</pre>
    </details> 
```

Enter fullscreen mode Exit fullscreen mode

这是扩展后的预览图:

[![Honeybadger Chatbot Example w/ Stacktrace](img/b20b44e2cb450a542ae3b71dbada7204.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eNeXdefS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kylekeesling.dimg/2019/07/honeybadger-chatbot2.png)

## 总结

我们仍然在 fulling 转移到 Basecamp 的过程中，但到目前为止，我没有像我想象的那样想念 Slack。

老实说，我们从 Slack 中错过的最多的是 GIPHY 集成和快速访问屏幕协作工具，所以如果你对你的团队如何处理这些领域有任何建议，[我很乐意听到这些建议](https://twitter.com/kylekeesling)。
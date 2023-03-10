# 脸书官方 API +聊天机器人第 2 部分:回声机器人

> 原文：<https://dev.to/takunda/facebook-unoficial-api-chatterbot-bot-part-2-the-echo-bot-16lo>

大家好，在这里我们将做一个简单的回声机器人，目的是测试 fbchat 库是否正常工作，以及释放即时因果报应...........

#### 回声机器人

回声机器人只是简单地回应给它的任何东西

你:孙
Bot:孙
你:月
Bot:月
你:禅
Bot:禅

[![Some random zen joke](img/621eacce032f08636a2420d68a440c72.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i-uR0fux--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.pinimg.com/originals/3c/18/c2/3c18c2c1ae018b6f2e830e7afe95184a.jpg)

简而言之，无论你付出什么，都会得到回报。好吧，让我们把手弄脏

##### 首要任务:导入库

`from fbchat import log, Client`

Client 是 fbchat 的主类，它包含了你用来和脸书交互的所有方法。您可以扩展该类，并覆盖 on 方法，以提供自定义事件处理(主要在侦听时有用)。

另一方面，日志记录只是用于日志记录

##### 子类化客户端类

`class EchoBot(Client):`

我刚刚死了，在轮回中，我决定成为客户的儿子，所以我继承了他所有的好基因(属性和方法)。我是多么幸运。

##### 覆盖 onMessage

onMessage 方法在每次发送消息时执行，无论是来自您还是其他人。然而，我们希望它以不同于父类的特殊方式执行，所以我们覆盖了它。

```
def onMessage(self, author_id, message_object, thread_id, thread_type, **kwargs):
        self.markAsDelivered(thread_id, message_object.uid)
        self.markAsRead(thread_id)

        log.info("{} from {} in {}".format(message_object, thread_id, thread_type.name))

        # If you're not the author, echo
        if author_id != self.uid:
            self.send(message_object, thread_id=thread_id, thread_type=thread_type) 
```

Enter fullscreen mode Exit fullscreen mode

##### 发生什么事了

*   我们收到一条信息
*   我们告诉发件人，他的信息已被传递和阅读
*   我们记录了一些我们收到的信息
*   如果发送消息的 id 不是我们的，我们就简单地回显消息

##### 最后但同样重要的是:登录并收听

```
client = EchoBot("nightfury@mars.example", "easypass123")
client.listen() 
```

Enter fullscreen mode Exit fullscreen mode

[![Ear cleansing](img/a12f58d568f7db7cfa1eb7f23ec3c1bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MoptS4mS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.askideas.com/media/15/Ear-Cleaning-Funny-Picture.jpg)

我们创建 Echobot 对象，然后进入监听模式。假设我们没有发送该消息，我们将返回我们接收到内容。如果 facebook 不禁止你，打破这个条件将导致一个无休止的回声循环。

拜拜
# 脸书非官方 API +聊天机器人第 3 部分:抓聊天机器人+集成

> 原文：<https://dev.to/takunda/facebook-unofficial-api-chatterbot-bot-part-3-scratching-the-chatterbot-integration-5aio>

大家好，在本教程中，我们将只是路过聊天机器人的地方，并说你好

#### 首先:导入库

```
from chatterbot import ChatBot
from chatterbot.trainers import ListTrainer 
```

Enter fullscreen mode Exit fullscreen mode

这一行将导入聊天机器人类和 ListTrainer，它们将用于训练机器人。

#### 创建一个机器人

```
chatbot = ChatBot("Adam") 
```

Enter fullscreen mode Exit fullscreen mode

#### 得到回应

```
response = chatbot.get_response("Good morning!") 
```

Enter fullscreen mode Exit fullscreen mode

#### 整码

```
from chatterbot import ChatBot
from chatterbot.trainers import ListTrainer

chatbot = ChatBot("Adam")
response = chatbot.get_response("Good morning!") 
```

Enter fullscreen mode Exit fullscreen mode

我给你们展示了一些非常简单的 fbchat 和 chatterbot 的片段。是时候将两者结合起来了

### 聊天机器人+ FBChat

[![Thanos joining two stones](img/c57a27c3d0c81d2f7f5ec63f66d63384.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CabQWslT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.images.express.co.uk/img/dynamic/36/590x/secondary/thanos-time-stone-1494507.jpg%3Fr%3D1536157631599)

所以每次有人给我们发消息，我们都想把它发送到聊天机器人，然后发送，然后回复给用户。那么我们该怎么做呢？

#### 导入库

```
from fbchat import log, Client
from chatterbot import ChatBot
from chatterbot.trainers import ListTrainer
from fb_creds import user_name, user_pass
import pickle 
```

Enter fullscreen mode Exit fullscreen mode

fb_creds 是存储凭证的文件。pickle 库将用于存储和检索会话 cookies。

#### 创建机器人

```
chatbot = ChatBot("Adam") 
```

Enter fullscreen mode Exit fullscreen mode

#### 看新的 onMessage

```
def onMessage(self, author_id, message_object, thread_id, thread_type, **kwargs):
        self.markAsDelivered(thread_id, message_object.uid)
        self.markAsRead(thread_id)

        # If you're not the author, echo
        if author_id != self.uid:
            # LINES FOR ECHOING 
            response = chatbot.get_response(message_object.text)
            message_object.text = response
            self.send(message_object, thread_id=thread_id, thread_type=thread_type) 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们使用 message_object 来获取文本消息，然后将它传递给我们的聊天机器人。在那里，我们将 message_object 文本属性更改为聊天机器人返回的任何内容，然后我们将它发送回发送者。

我们很聪明，对吗？

[![](img/851468080d4b16cf2016f3616bdcd3e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YTDU1Tvq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://reviewonline.co.za/wp-content/uploads/sites/68/2018/10/20180703190744-rollsafe-meme-520x350.jpeg)

不，你机器人还是被抛弃了。你睡觉的时候，你的机器人不能给你找新的女朋友。😔😔😔

拜拜
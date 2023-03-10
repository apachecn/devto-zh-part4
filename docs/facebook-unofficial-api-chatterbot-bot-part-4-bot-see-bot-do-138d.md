# 脸书非官方的 API +聊天机器人机器人第 4 部分:机器人见机器人做

> 原文：<https://dev.to/takunda/facebook-unofficial-api-chatterbot-bot-part-4-bot-see-bot-do-138d>

除非你教你的机器人新的技巧，否则它将一直是垃圾。

大家好。在本教程中，我们将教我们的机器人一些新的技巧

#### 训练你的聊天机器人

当我浏览网页时，我只找到了两种训练聊天机器人的方法:

*   使用 Listrainer 类
*   使用聊天机器人语料库

#### 使用 ListTrainer 类

##### 导入库

```
from chatterbot import ChatBot
from chatterbot.trainers import ListTrainer 
```

Enter fullscreen mode Exit fullscreen mode

##### 创建 bot 并开始训练

```
chatbot = ChatBot('Training Example')

trainer = ListTrainer(chatbot)

trainer.train([
    "Hi there!",
    "Hello",
])

trainer.train([
    "Greetings!",
    "Hello",
]) 
```

Enter fullscreen mode Exit fullscreen mode

就这么简单

#### 使用聊天机器人语料库

根据文件:

Chatterbot 语料库是包含在 chatterbot 模块中的对话数据的语料库。

语料库数据是用户贡献的，但是如果你熟悉这门语言，创建一个也不难。这是因为每个语料库只是机器人用来训练自己的各种输入语句及其响应的样本。

##### 安装聊天机器人-语料库模块

`pip install chatterbot-corpus`

在大多数情况下，默认情况下不会安装 chatterbot-corpus，所以您必须安装它。

##### 使用语料库

原谅我，我现在想到的是胼胝体。我一定是疯了，对吗？

```
from chatterbot import ChatBot
from chatterbot.trainers import ChatterBotCorpusTrainer

'''
This is an example showing how to create an export file from
an existing chat bot that can then be used to train other bots.
'''

chatbot = ChatBot('Export Example Bot')

# First, lets train our bot with some data
trainer = ChatterBotCorpusTrainer(chatbot)

trainer.train('chatterbot.corpus.english') 
```

Enter fullscreen mode Exit fullscreen mode

只要指定正确的机器人名称，所有这些都可以从一个单独的文件中完成。

#### 第三种方式

[![](img/0252598971e508fbdf57453b389e33e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9gyYzLfL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.istockphoto.com/photos/monk-walking-in-temple-meditating-under-a-tree-at-bangkokbuddhist-in-picture-id820391192%3Fk%3D6%26m%3D820391192%26s%3D612x612%26w%3D0%26h%3DNc1WLwkzxynsk5VgpZl4p28-51OvN_uzyyv2qzJSmIM%3D)

经过一些实验，我发现语料库并不聪明，你可以很容易地被知道你如何聊天的人发现。我们可以使用 ListTrainer，对吗？

[![Heart hell no](img/ab31abb4d98d389f2852ec1987135b82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LUrVIn4w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://sayingimages.com/wp-content/uploads/kevin-hart-no-memes.jpg)

您无法预测每一次对话，如果不花几个月或几年的时间来包括每一个可能的用例，也要花几天的时间。那么我们应该怎么做呢？诅咒 fbchat 和 chatterbot 作者？........不

如果我们让聊天机器人在制图时安静地做笔记会怎么样。我们聊天聊天，聊天机器人只是向老板学习。有一天当我们足够自信的时候.......我们释放野兽

[![](img/e902a36d502257346d42b91b3003556c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IAFYuqtO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.quickmeme.com/img/47/473564c4aace0860f8ddf9b92523404fc42405fb417651b5996e1d918bcb4a82.jpg)

下次见。下次教程再见

拜拜
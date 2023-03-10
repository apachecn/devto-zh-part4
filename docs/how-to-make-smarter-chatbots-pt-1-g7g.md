# 如何让聊天机器人“更聪明”，第 1 部分

> 原文：<https://dev.to/mcsh/how-to-make-smarter-chatbots-pt-1-g7g>

很多时候，当人们考虑在聊天机器人中使用人工智能时，他们认为他们需要创建一个复杂的算法来理解他们的用户想说什么，但他们无法从任何地方开始。我按照一个简单的想法创建了许多聊天机器人，其中一些拥有超过一百万的用户。

这里的想法很简单，每个对话至少有两个方面。人们倾向于过度分析事物，所以只要一点点帮助，你的聊天机器人就会显得聪明，如果它有解释的空间的话。考虑以下与伊莱扎的对话:

```
Men are all alike.
IN WHAT WAY
They're always bugging us about something or another.
CAN YOU BE MORE SPECIFIC
Well, my boyfriend made me come here.
YOUR BOYFRIEND MADE YOU COME HERE
He says I'm depressed much of the time.
I AM SORRY TO HEAR YOU ARE DEPRESSED
It's true. I am unhappy.
DO YOU THINK COMING HERE WILL HELP YOU NOT TO BE UNHAPPY
I need some help, that much seems certain.
WHAT WOULD IT MEAN TO YOU IF YOU GOT SOME HELP
Perhaps I could learn to get along with my mother.
TELL ME MORE ABOUT YOUR FAMILY 
```

大写的文本是 ELIZA，大小写混合的文本是 user。如你所见，伊莱扎看起来很聪明。它似乎知道用户在说什么，似乎在与他们交谈。但事实并非如此。ELIZA 通过在其输入中查找模式并将其转换为回复来进行操作。当她看到单词*相同*或*相同*时，她用什么方式回复*。另一种模式是，如果它看到与“我需要 X”匹配的东西，它会回答“得到 X 对你来说意味着什么”。如果 X 是*一些帮助*或*假期*，那就完全说得通了，伊莱扎甚至不知道假期或一些帮助是什么意思，它也不需要知道！但是考虑下面的对话框:* 

```
I need this like a hole in the head
WHAT DOES IT MEAN FOR YOU TO GET THIS LIKE A HOLE IN THE HEAD 
```

所以，尽管这种方法在 80%的情况下有效，但它并不那么有效。

另一个可以考虑的例子是 Woebot，这是一个聊天机器人，每天花 10 分钟和它说话会让你感觉更好。现在，Woebot 实际上使用一些先进的 NLU 来理解用户所说的话，但并不总是如此。他们唯一使用它的时候是当机器人问*你今天过得怎么样*时，然后它会试图理解用户是悲伤、快乐还是其他什么，并做出适当的回应。但是一旦这一步完成，它就进入了一种类似脚本的模式，这似乎真的很聪明，除非你的反应很糟糕。它甚至通过使用按钮而不是文本输入来限制用户的响应。

这种向用户提供按钮的响应在很多方面都很有帮助。它让用户的体验更流畅，他们不喜欢打字，开发者的生活更轻松，我们没那么喜欢 NLP/NLU。

这在 90%的情况下是可行的，但是有时我们需要更具体的输入，或者用户需要提供更多的信息。在这些场景中，我倾向于这样做，提示用户输入文本，如果可以的话，尝试验证它，然后做出相应的响应。

考虑问用户她的名字，这应该是一件容易的事情。首先我们询问用户她的名字，然后我们看它是否满足某些条件，例如，我们不期望在一个名字中看到特殊字符，然后我们只是用这个模板`Hi NAME!`欢迎他们

另一个例子是问用户今天过得怎么样。在这里，我们提示用户，*你今天过得怎么样？*然后我们按照这个模式创建一个响应:

*   将 ans 设置为空字符串
*   如果 NLU 检测到用户感觉良好，ans +=“很高兴听到这个！”
*   如果 NLU 检测到用户感觉不好，ans +=“很抱歉。”
*   如果 NLU 检测到用户在问我们好吗，ans +=“我很好，谢谢关心。”
*   ans +=“那么，我们来讨论一下.....”

这是一个非常简单的模式，你可以把它变得更复杂，而且它在大多数情况下都是有效的。我们也不需要担心，如果用户说他玩得很开心，而我们的 NLU 没有赶上，那又怎么样，我们还是会继续对话。但是如果我们碰巧抓到它，太好了！用户会感觉更好。

我将在下一篇文章中尝试介绍一些其他的技巧。感谢您的阅读！

你用什么让你的用户感到特别？你如何与他们交流，让你的机器人感到聪明？*
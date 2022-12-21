# 只有一种方法可以验证电子邮件地址

> 原文：<https://dev.to/jerodsanto/there-s-only-one-way-to-validate-an-email-address-4i3k>

验证电子邮件地址唯一可靠的方法是给它发一封电子邮件。你给它发个邮件！这是你做这件事的唯一方法。我知道你在想什么，

> “我有最好的正则表达式！”

> ![unknown tweet media content](img/17bb34948631eef23a0069ed631a2458.png)![Changelog profile image](img/9cc1b78e4325590eaf7604dfd6c28d8c.png)Changelog@ Changelog![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)任何足够高级的正则表达式都与魔术无异。02:17am-2017 年 3 月 26 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=845821937452486657)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=845821937452486657)79[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=845821937452486657)148

不，你没有。你以为你知道，但你不知道。您的正则表达式无效；还不够好。你知道那句老话:

> “一个开发人员在面临问题时，会想‘我知道。我将使用正则表达式。现在他有两个问题。"

这就是你所拥有的- <mark>你有两个问题</mark>。我知道这一点已经很多年了，但最近我仍然被说服添加一个基于正则表达式的电子邮件验证服务器端；

*(首先，永远不要相信客户，对吧？你可以在那里随心所欲，但它可以绕过你所有的检查。肯定是服务器端的。)*

> ![unknown tweet media content](img/ad97c146cd34401c87918838cf51d13e.png)![Changelog profile image](img/9cc1b78e4325590eaf7604dfd6c28d8c.png)Changelog@ Changelog![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)仅强制输入验证时客户端2017 年 2 月 23 日下午 18:25[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=834831478919987205)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=834831478919987205)1016[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=834831478919987205)1446

我放了一个基于正则表达式的电子邮件验证，我认为“这个非常好。”

事实上——伙计，我不知道我是怎么了；我甚至被说服复制粘贴一个要点！😭

它看起来相当不错，而且涵盖了大部分的基础，果然，上周我收到了一封潜在用户的电子邮件，说

> “嘿，我试图注册[每周更新](https://changelog.com/weekly)，但是它说我的电子邮件地址无效，而它<mark>显然是有效的</mark>，因为我现在就用它给你发邮件……”

我想，“我是个白痴。**为什么**我在系统上安装了基于正则表达式的电子邮件验证？”

所以不要那么做。我知道你可以在栈溢出上找到一个...我现在就告诉你，这还不够好。电子邮件地址太复杂了。有这么多有效的东西...

如果你要做的话——我承认我把它放在了里，但是我只是检查一下有没有一些东西，然后是一个`@`，然后是一些东西。

```
~r/^\S+@\S+\.\S+$/ 
```

这差不多就是你能做的...这只是为了确保你的数据库中不会有垃圾...🙅‍♀️

但是，你所要做的就是给他们发一封电子邮件，如果他们点击了，那就是一个有效的电子邮件地址。如果他们不点击，谁会在乎呢...？那是一个来之不易的教训！

如果您想要验证电子邮件地址，请向其发送电子邮件。问题解决了。

直到机器人开始点击电子邮件。那我们会有一个全新的问题...但到目前为止，我不认为有机器人会

1.  创建一个假的电子邮件地址
2.  报名参加你的东西，然后
3.  访问该电子邮件地址并点击链接

当我们到了那里，我们就必须想出别的办法。但在那之前，给它发个邮件就行了。

* * *

你刚刚读到的是来自 [JS Party #39](https://changelog.com/jsparty/39) 的摘录。为了可读性，我稍微修改了一下格式，但是这些(几乎)确切的话是我在那一集的[专业技巧片段](https://changelog.com/jsparty/39#transcript-98)中说的。除了像这样的提示，我们还[讨论新闻&趋势](https://changelog.com/jsparty/77)，采访[牛逼嘉宾](https://changelog.com/jsparty/71)，像我们 5 一样互相教东西[，并且从中获得很多乐趣。你应该每周四来参加我们的现场派对，或者订阅制作版本！听一听，让我们知道你的想法。💚](https://changelog.com/jsparty/76)

 [# 39:尝试一些新想法🔬](/jsparty/39-experimenting-with-some-new-ideas-)  [## JS 党](/jsparty) ![play](img/da55340508c383dd96223748725e7fff.png) ![pause](img/b2e500349dc3cd65ea44585f746d2a0d.png) ![JS Party](img/9be2c799f00ba0cbc824a6f52797d0e2.png)

<audio id="audio" data-episode="39-experimenting-with-some-new-ideas-" data-podcast="jsparty"><source src="https://cdn.changelog.com/uploads/jsparty/39/js-party-39.mp3" type="audio/mpeg"> Your browser does not support the audio element.</audio>

[![39: Experimenting with some new ideas 🔬](img/a3afdb93fb4ce5d8157822b6dd99b01e.png) ![animated volume bars](img/b4c6951c950232cdfef32ac750ed6d92.png) ](/jsparty/39-experimenting-with-some-new-ideas-)  ![play](img/da55340508c383dd96223748725e7fff.png) ![pause](img/b2e500349dc3cd65ea44585f746d2a0d.png)     ![volume](img/64c7607b77476eb3d924254262a5bca4.png)   <input type="range" name="points" id="volumeslider" value="50" min="0" max="100" data-show-value="true">    ![mute](img/53eef5aa80ab0610ec1ea90b95d54171.png)  1x  initializing... ×
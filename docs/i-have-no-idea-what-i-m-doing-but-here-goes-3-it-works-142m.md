# 我不知道我在做什么，但是第三条成功了！！

> 原文：<https://dev.to/jamonjamon/i-have-no-idea-what-i-m-doing-but-here-goes-3-it-works-142m>

唷！它确实有效。嗯，非常粗略。

如果你还没有跟上:1。你真可耻。2.为了学习 Node，我决定建立一个 API 来自动为我追逐自由职业者的工作。

我们说到哪了？试图从谷歌日历中获取任何内容并创建一个 JSON。这样做了，唯一的问题是——迭代的方式改变了数据，当同样的数据需要解释时，这是一件痛苦的事。因此，挑战在于保持由 Google API 提取的数据不变，并在需要处理时更改它，而不破坏原始对象。这样，它的任何部分都可以根据需要以任何方式使用。不知道这是不是正确的方法，但我觉得不错。这才是最重要的，对吗？

[github repo 有文件](https://github.com/jamonjamon/twc2k)。

我做的主要噩梦是理解下面的函数。我以为它回来了...但事实并非如此，是吗？它在迭代(不是吗？)通过每个事件并推动它们/console .一次记录一个。我想？不要把我的困惑误认为困惑。纯粹的恐惧是一个更好的描述。不管怎样，我认为如果我把它们放入一个数组，这可能是一个想法，而且似乎行得通。我要那个。

```
    if (events.length) {      
      events.map((event, i) => {
        const start = event.start.dateTime || event.start.date;
        cleanDate = start.slice(5, 10);
        //calData(start);
        array.push(cleanDate);
        //console.log(`${start} - ${event.summary}`);
        //calData(cleanDate);
      });

```

在那里，我只需要处理数据，将日期和月份分组，然后编写一个 if 语句，在满足条件时发送字符串。嘿，很快！一个机器人！

我给我的一个播音朋友看了，他疯了。“没门！它能做到吗？?"

是的西蒙。是的。它。加拿大西蒙现在认为我是个天才。我要那个。都是扯淡的因素，不是吗？

结束语大喊:
在[最后一篇帖子](https://dev.to/jamonjamon/i-have-no-idea-what-i-m-doing-but-here-goes-2-yay-json-5fk)中，我从尼尔·格林那里得到了一些很棒的建议。非常感谢你的提示，非常感谢。VScode 中的调试器让我大吃一惊。哇哦。！！一点脱胶有多好？？！！！

接下来:将此加入 gmail，并发送电子邮件。哦哦哦。有趣的时光就在前方。

对于那些一直不配合的:
[第 0 集](https://dev.to/jamonjamon/i-have-no-idea-what-i-m-doing-but-here-goes-0-4ffl)
[第 1 集](https://dev.to/jamonjamon/i-have-no-idea-what-i-m-doing-but-here-goes-1-decision-time-cp2)
[第 2 集](https://dev.to/jamonjamon/i-have-no-idea-what-i-m-doing-but-here-goes-2-yay-json-5fkb)
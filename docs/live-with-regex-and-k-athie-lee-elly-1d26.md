# 与 Regex 和/K 一起生活(athie Lee|elly)/

> 原文：<https://dev.to/iris/live-with-regex-and-k-athie-lee-elly-1d26>

[![Steve Jobs in a turtleneck sweater](img/652a010f1e50677c759fbc1a05d31f1c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G_Qf1Pf7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/61xywjckpxbcyru4htkd.jpg)

> “有人曾经告诉我，Regex 的世界会让我大吃一惊...我不是棚里最锋利的工具。”——史蒂夫·乔布斯

我写 regex 是因为我讨厌 regex。我写代码已经一年多了，每次我遇到正则表达式都会发生同样的事情——我感到寒冷和羞愧，赤裸地躺在地板上。我一直告诉自己，我会学习 regex 并变得更好，但我从来没有这样做...

直到现在。

首先要做的是-

不要像我一样。
[![Still of Jack Nicholson holding a small dog from the film "As Good As It Gets"](img/10f49cd9a6ee84244674ba667e66c94d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bWvO-e0E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/plwd6yhs9liv2uh0b791.jpg) 
不要等到一年后才去解决你正在苦苦挣扎的事情。

第二件事第二-

不要因为没有马上掌握一个概念而责备自己。

我个人是通过重复来学习的——我不应该逃避正则表达式，我应该去寻找它，一直练习到它有意义为止。

好了，现在，让我们来谈谈正则表达式，宝贝。

## 正则表达式

这篇文章旨在简要介绍正则表达式的主要概念。

正则表达式是用来查找和替换文本中匹配项的搜索模式。出于本文/博客/杰作的目的，我们将专门讨论正则表达式和 JavaScript。JavaScript 正则表达式可以使用正斜杠-
`let my_regex = /aaa/`
或使用 RegExp 构造函数-
`let my_regex = new Regexp('aaa')`来声明

正则表达式搜索模式由文字字符(例如:字母“a”)和具有特殊含义的字符(例如:星号*)组成。通过组合文字字符和特殊字符，regex 可以用来查找一段文本中的任何内容。加入 regex 的可选标志，你就可以前往*风味小镇*。

`Literal characters + special characters + regex flags = FLAVOR TOWN`

[![Picture of chef Guy Fieri](img/a7754391da4c2206dda404564e29b5ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zX4EMQwd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pk3ivhdqy9nzyhwlzj2f.jpg)

## 正则标志

正则表达式有 6 个标志...为了这篇文章的尊严，我决定不开明显的主题公园玩笑。我在这里做的是一件严肃的学术工作。

[![Serious expression Guy Fieri](img/c8cf345a77e6960f7ba02f8241b0dda4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v-Oj1Tna--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rtjx8g1v1zugue16a4xh.jpg)

| 旗 | 搜索类型 |
| --- | --- |
| g | 全球的 |
| 我 | 不区分大小写 |
| m | 多线 |
| s | 用“.”匹配换行符 |
| u | 采用双字节对字符进行编码 |
| y | 当前位置的粘性匹配 |

正则表达式标志总是列在正则表达式模式的末尾，如 so - `/matchymatch/i`。在这些标志中，全局搜索(查找字符串中的所有匹配项，而不仅仅是第一个匹配项)和不区分大小写的搜索(查找匹配项，不考虑大小写)是最常见的。

## 特殊字符

让我们来看看这些特殊字符及其用法。这一点没有包括在下表中，但是您也可以使用竖线|来表示或。

确保对正则表达式中的特殊字符进行转义(字符集除外)！

### 量词

量词允许您指定正则表达式的匹配数量。

| 特殊字符 | 使用 |
| --- | --- |
| * | 匹配 0 次或更多次(/a*b/匹配“aaab”、“b”、“ab”) |
| + | 匹配 1 次或更多次 |
| {n} | 精确匹配 n 次(修改为{n，m}以匹配 n 到 m 次) |
| ？ | 将其他量词从匹配最大次数修改为匹配最小次数 |

### 人物类别和界限

| 特殊字符 | 使用 |
| --- | --- |
| ^ | 匹配字符串/行的开头 |
| $ | 匹配字符串/行的结尾 |
| 。 | 匹配任何单个字符(换行符除外) |
| x(？=y) | 前瞻断言(仅匹配后跟 y 的 x) |
| (?！w) | 否定的前瞻 |
| [xyz] | 字符集-匹配字符集中的任何字符 |
| \b | 匹配单词边界 |
| \B | 匹配非单词边界 |
| \d | 匹配一个数字字符 |
| \D | 匹配非数字字符 |
| \s | 匹配空白字符 |
| \S | 匹配非空白 |
| \w | 匹配任何字母数字字符(包括下划线) |
| \W | 匹配任何非单词字符 |

完整的表格可在 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions#Using_special_characters) 上找到

## 正则表达式方法

下面是一些可以和 regex 一起使用的方法。

| 方法 | 描述 |
| --- | --- |
| 。比赛 | 返回匹配的数组 |
| 。试验 | 如果正则表达式匹配，则返回 true 或 false |
| 。使分离 | 使用匹配的正则表达式拆分字符串 |
| 。替换 | 用新的子字符串替换匹配项 |

```
my_regex = /K(athie Lee|elly)/
morning_show_one = "Live! with Regis and Kathie Lee"
morning_show_two = "Live! with Regis and Kelly"
my_regex.test(morning_show_one) // true
morning_show_two.match(my_regex) // ["Kelly", "elly"]
splitter = /and k./
morning_show_one.split(splitter) // ["Live! with Regis ", "lly"]
morning_show_two.replace(my_regex, "Michael/Ryan") // "Live! with Regis and Michael/Ryan" 
```

Enter fullscreen mode Exit fullscreen mode

## 总之...

关于 regex 还有很多需要学习的地方，这只是冰山一角。

我在这篇文章的研究中使用了一些很棒的资源，我强烈推荐你去看看:

*   [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)
*   [正则表达式信息](https://www.regular-expressions.info/tutorial.html)

这就是这次旅行的全部内容，不过别担心，网上还有很多好玩的地方。下次我会在台式机、开发人员和驱动器上与您联系！
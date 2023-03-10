# 实时编码学习-2019 年 6 月 18 日

> 原文：<https://dev.to/jitterted/live-coding-learnings-june-18-2019-6nb>

> 我在[https://twitch.tv/jitterted](https://twitch.tv/jitterted)今天的现场编码流中学到的总结。

## 匹配正则表达式中的换行符

对于我正在处理的文本，我希望正则表达式搜索和替换跨换行符工作(默认情况下匹配在新的一行停止)。今天我了解到我需要将这个:`(?s)`前置到 regex 字符串，这会打开`DOTALL`模式，因此点通配符将跨新行匹配。有关详细信息，请参见 JavaDoc:[https://docs . Oracle . com/javase/8/docs/API/Java/util/regex/pattern . html # DOTALL](https://docs.oracle.com/javase/8/docs/api/java/util/regex/Pattern.html#DOTALL)

### `matches()`使用`DOTALL`的例子

我想搜索开头和结尾都被 3 个等号(`===` )
包围的文本。文本可以跨多行。

例如:

```
Before text.
===
Text inside the equals signs.
===
Outside text. 
```

为了搜索文本，我将使用`String.matches()`方法，就像这样:

```
boolean found = text.matches("(?s)===(.*?)==="); 
```

如果找到了模式，它将返回`true`，就像上面的
示例一样。

### `replaceAll`使用`DOTALL`的例子

如果我想用一个`<pre>`标签替换三个反勾号代码块(就像我在我的实时流中所做的那样)，那么我会这样做:
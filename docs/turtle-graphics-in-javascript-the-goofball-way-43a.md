# JavaScript 中的乌龟图形

> 原文：<https://dev.to/wikijavascript/turtle-graphics-in-javascript-the-goofball-way-43a>

[http://slicker.me/javascript/turtle.htm](http://slicker.me/javascript/turtle.htm)

例如，Logo(自 20 世纪 60 年代以来)和 Python 中提供了海龟图形，但 JavaScript 中没有。
或者是？我突然意识到，context.rotate 基本上模拟了“左转/右转”，而 context.translate 与 moveTo/drawTo 结合起来的行为就像“向前/向后移动”。这绝对不是一种优雅或可扩展的图形编程方式——有点像用蚯蚓系鞋带:它看起来很酷，但只在特定条件下有效。只是我收集的奇怪代码中的另一个小发明。如果你想玩真正的海龟图形，我建议你使用许多可用的 JS 库之一，Python 或 Logo。或者至少创建移动和旋转海龟的实际函数，使代码可读。
# 结构回调。

> 原文：<https://dev.to/adam_cyclones/struct-callback-m7j>

我正在写一个定义为 bTreeMap 的语法，关键字是令牌，值是语法定义结构。所讨论的结构存储关于特定标记的元数据，例如错误名称、关于标记后面允许什么的规则等等。我希望能够有选择地设置一个回调来处理所述元数据的错误和规则。这是最佳模式吗？在代码中会是什么样子？任何帮助都将不胜感激，我已经谷歌了大约一个月。
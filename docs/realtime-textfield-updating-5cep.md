# 实时文本字段更新

> 原文：<https://dev.to/midhetfatema94/realtime-textfield-updating-5cep>

我刚刚注意到，在启用了字符计数的 textview 中，计数总是比实际文本少几个字符。

这里的问题是，输入的完整字符串不会在文本字段的文本属性中更新，直到它在其生命周期中从范围内的更改字符函数返回 true。
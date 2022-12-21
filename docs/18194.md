# 如何在 regex 中写“四个字母的单词”？

> 原文：<https://dev.to/realquadrant/how-to-write-four-letter-word-in-regex-89p>

嗨，我正在写一篇博客，想加入一些幽默(不知道是否可行)，但基本上是引用典型的“取 x 的 s**t our ”,并用正则表达式替换四个字母的单词。

这会是清晰的，但也是准确的吗？

`Taking the /s[a-z]t{4}/ Out of CLAs`

我不确定是应该使用{d}还是使用\b 作为边界的开始和结束。

是否应该更加明确，并让它匹配第一个字符是`s`而最后一个字符是`t`？

欢迎其他建议！
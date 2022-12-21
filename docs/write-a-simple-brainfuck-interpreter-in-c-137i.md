# 用 C#写一个简单的 Brainfuck 解释器

> 原文：<https://dev.to/tingwei628/write-a-simple-brainfuck-interpreter-in-c-137i>

[GitHub](https://github.com/tingwei628/bfcs)

我听了太多次 [Brainfuck](https://en.wikipedia.org/wiki/Brainfuck) ，决定花些时间用 C#开发一个简单的解释器。如果你想知道 Brainfuck 是如何工作的，请查看[brain fuck visualizer @ fatiherikli](http://fatiherikli.github.io/brainfuck-visualizer)。

~~这里有一点要提一下。当我试着在`Nested loop`上工作的时候，我用`Stack` (FILO)来记忆支架的位置。这有助于订购支架和导航支架的准确位置。因为我只测试了像“Hello World”这样用 Brainfuck 编写的基本案例，所以我需要覆盖更多的测试案例。~~

接下来，我会尝试验证 Brainfuck 文件。:)
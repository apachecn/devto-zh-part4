# Javascript ES6:使用默认值进行析构

> 原文：<https://dev.to/prototyp/javascript-es6-destructuring-with-default-values-2aj6>

这是一个简短但非常有用的代码片段，用于用默认值(如果值未定义)析构变量。对于避免由`undefined`变量引起的错误非常有用的片段。

在下面的例子中，`options`对象正在被析构。

```
const {
    valFirst = 1,
    valSecond = "hello",
    valThird = false
  } = options; 
```

Enter fullscreen mode Exit fullscreen mode

如果一个析构值不存在于对象中，它将被赋予一个默认值。如果它确实存在于对象中，它将被赋予来自对象的值。

感谢你花时间阅读这篇文章。如果你觉得这很有用，请给它一个❤️或🦄，分享评论。
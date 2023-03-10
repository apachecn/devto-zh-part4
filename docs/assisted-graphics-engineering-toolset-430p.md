# 辅助图形工程工具集

> 原文：<https://dev.to/wonglok/assisted-graphics-engineering-toolset-430p>

[![Assisted Graphics Engineering](img/8a249b1368ebfbe4478c946677472266.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fM9bOLGh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6mngu76d8nz4x4x67a98.png)

辅助图形工程工具集(A.G.E .)实际上是我的旧概念，叫做 ShaderNode。它基本上是功能树中 GLSL 的一个基于节点的再混合器…

但是在通用电气公司，它有多个输出。

我构建着色器节点/年龄系统有几个原因

1.  GLSL 很难仅用代码来猜测输出。

2.  GLSL 对于很多 Web / JS 开发者来说是陌生的。模块化系统可能会有所帮助。

3.  在 GLSL 市场的早期阶段，调试相当困难，因为当出现一个小错误时，你得到的只是一个空白屏幕。不过还好，多亏了三个。JS 的错误调试线系统。现在调试更容易了。但是生活中的每件事都有改进的空间。

4.  正常设置没有 GLSL 每次按键的实时更新。你可以依赖 chrome 的第三方插件或者使用 fire fox 调试器。

5.  设置 GLSL 执行不像简单的网页访问那么简单。

6.  执行隔离对于调试和基本分析，当我们断开模块时，它不会被执行，甚至不会包含在执行环境中。

7.  模块的参数/ IO 的数据类型。避免参数类型的输入错误。

[a . g . e .](http://age.wonglok.com)
T3】Github

我很想有一个对话的呼吁，为你的作曲 GLSL 的方式。请让我知道...

1.  glsl 怎么写？你使用任何软件来辅助 glsl 的开发过程吗？
2.  你开发 GLSL 的创意过程是怎样的？
3.  你觉得我应该尝试编码/探索开发经验的可能性。
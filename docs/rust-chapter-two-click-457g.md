# 铁锈日志:第二章，终于点开了！

> 原文：<https://dev.to/adam_cyclones/rust-chapter-two-click-457g>

在与编译器斗争的同时，我设法编写了一个解析器，可以反向解析 JavaScript 的子集。

我花了很长时间研究解析器理论和 rust 框架，但最后我只是说，管它呢，我会为这个项目写一些对我有意义的东西。我正在构建一个带有回调的令牌流，我的灵感来自 Marpa parser over peg grammer，所以它看起来像 Marpa 作为一个解析器组合子。

这个想法是将 JavaScript 和 CSS 混合到一个文件中，你可能还记得我在 scss 中关于 JavaScript 增强混合的 Jess 帖子，我正在为 cli 编译器构建 libjess，然后为 wasm 构建一个反应式 VM。这是一件令人头疼的事情，也是一件非常有趣的事情。当我有进一步的进展时，我会通知你。
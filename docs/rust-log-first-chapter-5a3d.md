# 生锈日志:第一章

> 原文：<https://dev.to/adam_cyclones/rust-log-first-chapter-5a3d>

这是我进入 rust 的第一步，我已经观察 rust 有一段时间了，不知道怎么会有人学习这个，来自 JavaScript / Typescript 的语法很危险。

我写了一个 WASM 项目，其中 90%是针对 node Js 的 Lua VM，我花在学习现代 c++上的时间比我实际写 Lua 的时间还多。这极具挑战性，也很有趣。

虽然这个项目对我来说很重要，但它不会帮助任何人，除非 Lua 热潮兴起，或者游戏工作室想用 node 和 Lua 制作游戏。我将完成这个项目，但我也在为 Jess 和 Tess 工作，在社区的良好反应之后，我已经开始在 rust 中为 Jess 编写 cli。

第一天我用了一个叫做 clap 的框架来解析 args，我没有时间写一些 cli 库，我需要得到一个 MVP。我很快就陷入了验证 Jess 文件是否存在的困境。这很令人沮丧，因为在 node 中，我可以在一行代码中完成这项工作，并处理大量边缘情况。Rust 可以防止你编写糟糕的内存管理代码和糟糕的代码，但是如果你正在学习，这将是一场噩梦。我发现自己在大喊“我恨你，让你的生活变得如此平静！”因为最终错的不是铁锈而是我。

我坐下来学习了关于借用和所有权的知识，现在事情正在升温，有了这些知识，我强烈建议你也这样做，因为这并不痛苦，我已经制作了一个工作的 cli，它的设计原则如果不是因为 rust 的严格性，我甚至不会想到。我会让你知道事情的进展。
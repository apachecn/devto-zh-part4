# 有效生锈的工具

> 原文：<https://dev.to/msfjarvis/tools-for-effective-rust-development-3mb4>

> 2022 年 2 月更新:这篇文章写于 2019 年，因此围绕 Rust 的许多工具景观已经变得更好。这篇文章的新版本正在制作中，完成后会链接到这里。

Rust 是一种内存安全的系统语言，速度极快，没有运行时或垃圾收集器开销。它可以用来构建非常高性能的 web 服务、CLI 工具，甚至是 [Linux 内核模块](https://github.com/fishinabarrel/linux-kernel-module-rust)！

Rust 还提供了各种各样的工具，让开发变得更快、更用户友好。我将在这里回顾一些我个人使用过的，并且发现很神奇的东西。

## 货物-编辑

[cargo-edit](https://github.com/killercup/cargo-edit) 是一个机箱，它扩展了 Rust 的 cargo 工具，带有`add`、`remove`和`upgrade`命令，允许你轻松管理依赖关系。[文档](https://github.com/killercup/cargo-edit/blob/master/README.md#available-subcommands)详细介绍了这些选项。

我个人觉得`cargo-edit`在有很多依赖项的项目中很有用，因为手动寻找更新版本很烦人。

## 货物夹

[cargo-clippy](https://github.com/rust-lang/rust-clippy) 是 Rust 的高级棉绒，它将**331**(撰写时的[)不同的棉绒集合在一个包中，由 Rust 团队建造和维护。](https://rust-lang.github.io/rust-clippy/list/index.html)

我发现除了官方文档和[“这本书”](https://doc.rust-lang.org/book/)之外，它还是一个很大的帮助，可以用来编写更干净、更高效的 Rust 代码。作为一个初学 Rustacean 的人，我发现这非常有助于脱离我在其他语言中的模式，在我的代码中使用更多的“rust-y”结构和表达式。

## 拉斯特法特

[rustfmt](https://github.com/rust-lang/rustfmt) 是 Rust 代码的官方格式化工具。这是一个自以为是的、零配置的工具，而且“非常好用”。它还没有达到`1.0`版本，这需要一些[警告](https://github.com/rust-lang/rustfmt#limitations)它的使用，但以我的经验，它将为大多数人和代码库工作，没有任何麻烦。

作为一名 Kotlin 程序员，我非常习惯于拥有一个官方的风格指南来保证所有项目的格式一致。给 Rust 的开发带来了同样的便利，这很重要，因为 Rust 没有任何官方的 IDE 可以自动完成这项工作。

## rls

[rls](https://github.com/rust-lang/rls) 是 Rust 对微软[语言服务器协议](https://microsoft.github.io/language-server-protocol/)的实现，试图标准化语言工具和 ide 之间的接口，以允许代码完成、查找 hover 上的所有参考和文档等功能在不同的 ide 之间无缝工作。 [VSCode](https://code.visualstudio.com/) 实现了`language-server-protocol`，并使用 [rust-lang.rust](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust) 扩展与`rls`无缝集成，以创建引人注目的 IDE 体验。

作为一名初学者，能够在编辑器中检查代码，并且不需要为每次更改进行编译，这是学习和开发过程中的一个巨大进步。关于 crates 和 errors 的文档可以直接在 hover 上获得，这无疑有助于我进一步了解和理解这种语言。

## 结论

因此，这是我的必备工具清单，它帮助我作为一名农村人不断提高。很好奇听听别人在用什么！我选择尽可能坚持使用官方工具，因为它们已经被证明非常可靠，而且我似乎可以在网上找到更多的帮助，但是我很乐意尝试提供显著优势的非官方替代工具:)
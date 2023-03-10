# Rust 能取代 C/C++成为系统语言吗？

> 原文：<https://dev.to/kendru/can-rust-replace-c-c-as-a-systems-language-7c7>

我一直在 YouTube 上观看 [CMU 高级数据库系统](https://www.youtube.com/playlist?list=PLSE8ODhjZXja7K1hjZ01UTVDnGQdx5v5U)的课程讲座，并在观看的同时尝试在 Rust 中实现数据库的一些部分。Rust 让一些高级代码变得如此简单，这给我留下了深刻的印象(谢谢，模式匹配，枚举，选项类型等等)。由于 Cargo、rustup 和 Clippy，工具也令人难以置信。然而，编写数据库、内存分配器、设备驱动程序和其他经典“系统”软件所需的代码有时似乎很困难。

有保证的内存安全可能不利于以管理内存为核心需求的软件。例如，数据库需要将值打包到连续的内存中，并在运行时解释数据的类型。这并不太难，但是需要使用`unsafe`代码——并且失去了 Rust 编译器的内存安全性。Rust 的内存安全在一种低级编程语言中是否可取？防止 bug 的负担应该放在编译器的什么位置，应该放在开发人员和一套好的单元测试的什么位置？

我不喜欢 C++，但我想知道它是否能简化低级软件的实现。
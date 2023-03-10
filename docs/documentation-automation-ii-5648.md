# 文件自动化 II

> 原文：<https://dev.to/elcotu/documentation-automation-ii-5648>

嗨 devs，

几周前，我写了这个帖子:

[https://dev.to/elcotu/documentation-automation-p8m](https://dev.to/elcotu/documentation-automation-p8m)

几个星期后，我认为分享我一直在尝试、探索和构建的自动化文档工作流是有用的。

起初，我一直在学习 markdown，因为它似乎是一种创建文档源的好语言。
然后发现了 [Pandoc](https://pandoc.org/) 工具。这是一个开源软件，旨在将文档从 markdown 翻译成几种格式(*。docx，*。odt，*。pdf，*。html，*。epub 等等)。这实际上是我所需要的，但是，如何实现它呢？

该工具适用于 Linux 和 Windows。我是一个 Linux 新手，我必须承认。但是通过在两种操作系统中进行测试，我很快发现，与 Windows 相比，Linux 可以在命令行中做更多的事情(是的，许多人会认为“当然”)。所以我开始用 markdown 文档创建 git 项目，并尝试自动化文档生成。迟早，我会看到自动化的一系列用例:

*   **每个子文件夹一个文件**:从当前工作目录的每个子目录中的每个文件的内容创建一个带有目录的 docx 文件。这意味着处理一组 how-to 文件的生成，每个文件都有一个图像子文件夹

*   **Filtered doc** :从包含给定字符串的目录中的每个文件的内容创建一个带有目录的 docx 文件，然后发布到 Dropbox 的一个文件夹中。这意味着编译一个包含所有已批准或申请批准文件的文件

*   **改变控制**:遍历一个目录。用每个文件的提交日志创建一个名为 cdc.md 的文件。然后创建一个*。docx 文件，原始文件的内容和 cdc.md 文件附加在它的末尾。这意味着创建具有修订历史的文档文件，以符合 ISO 程序

然后所有情况都可以使用其他输出格式。

然后我思考并着手下一步。基本上，在某个地方发布文件。比如 Dropbox、FTP 或任何可以共享静态内容的地方。

这是一个存储库，我在这里编译了一些 Pandoc 示例: [Pandoc 示例](https://github.com/dcoturel/pandoc-samples)

有人以某种方式使用过这样的工具或这些需求吗？

你好，
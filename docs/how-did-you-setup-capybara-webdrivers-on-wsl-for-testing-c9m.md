# 你是如何在 WSL 上设置 Capybara + Webdrivers 进行测试的？

> 原文：<https://dev.to/amorpheuz/how-did-you-setup-capybara-webdrivers-on-wsl-for-testing-c9m>

我最近开始对 dev.to 的 GitHub 库进行重构。js 文件来满足 eslint 规范。为了不破坏某些功能，一些需要重构的文件需要同时重构多个其他文件。

为了检查这一点，我一直在尝试设置文档+中指定的验收测试，并通过一些在线帮助来检查是否所有功能都保持原样。但是我一直在遇到各种各样的障碍，要么是 chrome 二进制文件找不到，要么是安装后发布了`Invalid Session id`，要么是有时它完全可以工作。目前对我来说还是个谜。

任何正确方向的帮助/指针都将不胜感激:d .愿意提供同样所需的任何细节！
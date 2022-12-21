# 为什么 Slack 要改变它的内部？

> 原文：<https://dev.to/mykeels/why-is-slack-changing-its-internals-4p4c>

[由](https://dev.to/changoman/easy-dark-mode-for-slack-1mmn) [@changoman](https://dev.to/changoman) 发表的这篇文章启发了许多改变 Slack 视觉主题的工具。

以前很容易找到并编辑 Slack 的 ssb-interop.js 文件，加载一个定制的 CSS 来改变它的外观。

在 v4.0 的最新版本中，这个文件消失了，它作为一个打包文件存在于一个压缩文件`app.asar`中。

为了实现相同的功能，您必须将`app.asar`提取到一个文件夹中，编辑`dist/ssb-interop.bundle.js`文件，并在重启应用程序之前将文件夹压缩回`app.asar`。

这意味着 Slack 的程序从压缩文件中加载内容。

我想知道这样的安排对 Slack 的内部架构有什么好处。有人知道吗？
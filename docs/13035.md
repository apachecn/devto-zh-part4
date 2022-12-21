# 我真的想写一个 Google Keep 工具吗？

> 原文：<https://dev.to/bugmagnet/do-i-really-want-to-write-a-google-keep-tool-2a82>

在过去几年写了这么多包装器和这么多抓取工具之后，我现在问自己:“我真的想为 [Google Keep](https://keep.google.com) 写工具吗？”有一些人呼吁 Google Keep API，但目前还没有任何东西实现(AFAICT。)

我确实使用 Keep a lot 作为有趣的网络内容的总称。请注意，我也使用[口袋](https://app.getpocket.com/)。还有[梨树](https://www.pearltrees.com/)。还有 [Zim](https://zim-wiki.org/) 。

摆弄我在 [Firefox 开发者](https://www.mozilla.org/en-US/firefox/developer/)中的 Keep 集合，看到我试图找出一些可以在 [Selenium](https://www.seleniumhq.org/) 中定位的结构。一个感兴趣的项目是通过`document.querySelectorAll(".RNfche")`。此时，我不知道 RNfche 类是特定于我的集合还是每个人的集合，但它存在于每个包含它的 DIV 中。随着页面的向下滚动，querySelectAll()的结果数会增加。因此，要找到它们中的每一个，就必须编写 Selenium 来滚动到集合的底部，然后再拉起`RNfche`类元素的列表。

我真的会被打扰吗？
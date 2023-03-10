# 点击时弹出对话框

> 原文：<https://dev.to/catchen/mailchimp-popup-dialog-on-click-4h08>

## 问题

MailChimp 以弹出对话框的形式提供注册表单，但对于何时触发它的选项有限。可用的触发器有“页面打开后立即”、“用户滚动到页面底部时”等。当读者点击我博客上的注册链接时，我想触发对话。我不想导航到注册页面，而是想打开对话框，加快体验速度。希望这样可以提高订阅率。

## 解

MailChimp 为弹出对话框提供了一个 HTML 代码片段。它包含两个`<script>`标签。我没有改动第一个，修改了第二个。阅读下面的代码以供参考:

在第一个`<script>`标签中，我什么也没做。在第二个`<script>`标签中，我将原始 JavaScript 封装在一个名为`displayDialog`的函数中，该函数将在注册链接的点击事件中被调用。我添加了第三个`<script>`标签来搜索页面中的注册链接，并添加 click 事件处理程序。

在`displayDialog`函数中，我不仅调用了第二个`<script>`标签中的原始代码，还删除了之前的两个 cookies。这是因为当用户关闭对话框或通过对话框订阅时，MailChimp 会设置这些 cookies 中的一个。其中一个 cookies 的存在将阻止对话框再次打开。当使用 MailChimp 的自动触发器时，这种行为是有意义的——用户在取消或订阅后不应该再看到对话框。当触发器是用户点击一个链接时，这是没有意义的，因为这是一个打开对话框的明确意图。**(如果你想重用我的代码，记得用你自己 MailChimp 活动的代码替换`window.dojoRequire(...)`。)**

第三个`<script>`标签是为我自己的博客定制的。它查找任何指向注册页面的链接，并添加 click 事件处理程序。事件处理程序调用`displayDialog`来触发对话框，然后取消浏览器到注册页面的导航。

如果你想测试这个功能，确保你从我的博客中打开这篇文章，然后点击这个链接来订阅。它应该打开注册对话框，而不是注册页面。如果你喜欢这篇文章，记得把你的电子邮件和订阅！

[![](img/4475472da1af5a8f26ad925aaaf43ae9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PzXxuNG8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/CatChen/English/%257E4/w6zmPW0iFsQ)
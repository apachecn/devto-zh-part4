# 从 jQuery 恢复

> 原文：<https://dev.to/edlinkiii/recovering-from-jquery-26fg>

10 多年前开始学习 jQuery。我需要实现 Ajax，让页面更加动态——或者说，有一天我的老板在会议上提到了这一点。我从石器时代(1996 年)就开始摆弄 HTML，并开始学习 PHP/MySQL，因为我们的简单网站必须要有它。

但是 JavaScript 呢？真的吗？我*讨厌*它！在 IE 4/Netscape 4 浏览器大战期间，我试图用它做一些事情，但这并不容易，我真的不想弄糟它。**有史以来。**

我找到了我能找到的最简单的 Ajax 调用设置(我想大概有 20 行代码)，并开始向 PHP 发出请求。我用 PHP 渲染了我通过`.innerHTML`传回和转储的所有 HTML...啊，美好的旧时光。(lol)

从那以后发生了很多变化，主要是发现和学习 jQuery——这让我有了现在的职位。

我一点也不知道我的这个 Backbone.js/jQuery 项目走进了一个马蜂窝。它已经投入生产一两年了，我被告知在任何情况下都不要更新任何现有的库，尤其是 jQuery(因为在过去，所有的东西都会坏掉)。

这一切都很好，直到一个客户对我们的系统进行了漏洞扫描。哎呀！我现在被告知必须更新 jQuery(当时是 1.7)，以及所有可以更新的库。女士们先生们，这一点都不好玩。

我已经开始玩 React，并正在寻找 Angular。我发现 jQuery 并不是人们现在真正(有意)使用的东西，普通的旧 JavaScript 比以前更好(也更容易使用)!

现在我们所有的库都已经更新了，我们的潜在客户就是我们的实际客户，我决定尽可能多地重构我们的视图，不使用 jQuery。我开始只是使用一些箭头功能，将`var`改为`let||const`，将`$`改为`document.querySelector`，将`.html()`改为`.innerHTML`。我目前正在编写迷你库来替换 jQuery UI(以及其他)。

用这种方式做事，我学到了很多东西。(我学到的第一件事是*我不懂 JavaScript，我只知道 jQuery。*)

然而，如果我不给你留下一些真正帮助我的资源，那我就失职了:

*   [从 jQuery 移动](https://gist.github.com/erlendmr/7935726)
*   [(现在比以往任何时候都需要)你可能不需要 jQuery](https://css-tricks.com/now-ever-might-not-need-jquery/)
*   [从 jQuery 到 JavaScript:参考](https://code.tutsplus.com/tutorials/from-jquery-to-javascript-a-reference--net-23703)
*   [MDN :: JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript) (无价！)

我很想看看其他人整合了哪些资源/方法来做出这种改变。请用你的故事/路径/无论什么来评论！

*请不要抱怨没有早点升级。

请查看 [Refactoring jQuery](https://dev.to/edlinkiii/refactoring-jquery-2klg) 和我一起继续这段旅程。
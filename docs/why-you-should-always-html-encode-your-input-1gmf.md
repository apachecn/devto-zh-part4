# 为什么你应该总是 HTML 编码你的输入

> 原文：<https://dev.to/tim_tr/why-you-should-always-html-encode-your-input-1gmf>

你刚刚建立的网站可能是一个漂亮的、编码良好的杰作，但是如果没有这段代码，它崩溃的时间不会很长。

你可能已经听说过这个话题:网站安全。我将向你展示一个简单的技巧，它可以防御那些试图使用“xss”(跨站脚本)入侵或利用你的网站的黑客。

当然，这只是一个基本的对策，如果你想知道更多，谷歌将指导你输入 xss) .此外，这是我在这里的第一个帖子，所以任何关于我的帖子的意见或建议都是受欢迎的。

现在来看更有趣的东西:

比方说，在你的网站上有一个区域，你要求用户输入，并向他们显示确切的输入(想想博客上的评论，等等)。如果我们不净化用户给定的输入，我们会有很多问题。

例如，如果用户输入“alert( '哎呀')”会怎么样？网站将显示的评论将包含那个实际上什么也不显示的字符串(一个空评论)，但它将做的是给页面上的每个用户一个弹出窗口，说“哎呀”。
现在，这一切都很有趣，还不太严重，因为黑客也可能开始加载恶意脚本并开始修改页面。

现在，看看这个小测试网站:

[![](img/e01122b5b6dc47f7ce2e22362ff6eae8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UiaiXEzV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/goqtw6tgeefbjn2aghs2.PNG)

该网站会将用户输入的任何内容输入到输入框中，并显示在该框下方的 div 中。

[![](img/08534f3261d23239bffc43992a69a78c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rfEqmhPn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ccrrxmvz9assz7g2o9ws.PNG)

在 PHP 中净化输入非常简单，事实上我们只需要使用 1 个函数:htmlspecialchars()；
看看下面的代码片段:

[![](img/a6738b0dceb215d806c65c079f73f237.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vNbBtD8X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6svlebabbu8tcao1023l.PNG)

现在我们来测试一下。

[![](img/a5c722aeeb46b12745ad8506d0f1fa3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6hemHC_0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x6kyn043j1kqvzk2wen7.PNG)

[![](img/3740cee79def50f931cd30701c91a0ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AzOoBD5e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a0mbcw049vlmr1h5fz78.PNG)

但是幕后发生了什么？

[![](img/fa8744b0c6384f3804c4886530d22108.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M-SEVVi1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pih57gzjjfi8ts2upnmo.PNG)

PHP 将''改为' >；'。现在 HTML 可以正确地显示这些字符，而不认为它是一个实际的标签。

所以我们开始了，我们向更安全的网站迈出了第一步！

感谢您花时间阅读我的帖子！
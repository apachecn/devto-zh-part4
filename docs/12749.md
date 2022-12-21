# 优化网站代码以加快加载速度的 5 个工具

> 原文：<https://dev.to/samueltaylormps/10-tools-to-optimize-the-website-code-for-faster-loading-2m1>

首先，互联网正在向更快的网站发展。每个网站所有者都希望他们的网站加载速度尽可能快。作为开发人员，在不破坏代码的情况下进行优化总是一个挑战。

我在开发领域工作了五年，从我的经验来看，我可以说我们开发人员现在得到了一个额外的 tsk，叫做“优化”

在这个主题中，我将列出一些我们可以用来测试网站加载时间的工具。

1.  谷歌页面速度[https://developers.google.com/speed/pagespeed/insights/](https://developers.google.com/speed/pagespeed/insights/)

这个工具是 Google 做的，由 lighthouse API 提供支持。这个工具告诉你在网站中要优化什么，比如图片，CSS，JS，或者任何 HTML 的东西。大多数人在处理“渲染阻塞错误”时都会遇到困难我在下面添加了一个发布修复的链接。

[https://www . WP beginner . com/WP-tutorials/how-to-fix-render-blocking-JavaScript-and-CSS-in-WordPress/](https://www.wpbeginner.com/wp-tutorials/how-to-fix-render-blocking-javascript-and-css-in-wordpress/)

1.  Pingdom

这是另一个测试你的网页速度的工具，并列出可以优化的地方。一些优化存在于代码中，一些存在于服务器级别。然而，它们很容易。瀑布图是最棒的。它精确地显示了哪个文件/类型使你的页面加载速度变慢。深入研究并优化。

1.  gt metrix[https://gtmetrix.com/](https://gtmetrix.com/)

Gtmerix 是另一个速度测试工具，可以替代 Pingdom。虽然两者都是免费的，但为了更准确，还是要在两者上测试你的网页。问题是它告诉 js 需要优化，但从来没有告诉哪一个。

我喜欢 GTMetrix 的时间线图；您可以分析网页的哪个部分加载时间较长。

1.  谷歌浏览器开发工具。

如果你不想使用任何第三方工具，你有 chrome 开发工具。chrome dev 工具最好的部分是，它告诉你哪些 CSS、HTML、JS 代码没有被使用，而是放在页面上。

有时我们会在所有页面中放置脚本和 CSS。并非所有页面都使用了所有的类或 id。为什么使页面额外的重量有未使用的代码？

这就是为什么我总是喜欢为每个页面创建单独的 CSS/ JS。我对页面权重很偏执。

1.  网页测试

[https://www.webpagetest.org/](https://www.webpagetest.org/)

另一个工具几乎提供了与 gtmetrix 和 Pingdom 相同的功能，但它们是更好的 TTFB(加载第一个字节的时间)分析工具。我们知道一些后端功能会阻止页面加载。这增加了 TTFB。通过 webpagetest，我们可以测量并修复它们。

快乐优化！！

原谅我的语法错误；我只想尽快完成。

我在 https://mypaintsprayer.com 工作。在 sammy@mypaintsprayer.com[随时给我打电话](mailto:sammy@mypaintsprayer.com)
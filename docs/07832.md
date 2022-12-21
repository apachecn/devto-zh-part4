# 星期五前端:令人愉快的 CSS 快速点击版

> 原文：<https://dev.to/kball/friday-frontend-delightful-css-quick-hits-edition-3b4k>

星期五快乐！我回到了家，在热浪中煎熬，慢慢从时差中恢复。:P 至少这周有一些很酷的文章！

特别是，我想把你的注意力吸引到前两篇 CSS 文章上——它们在概念上都非常简单，但是第一篇文章非常简单地解决了许多常见的 UI 问题，而第二篇文章实现了令人愉快的效果。还有一些很棒的 JavaScript 文章，一个关于浏览器原生延迟加载的超级令人兴奋的公告，以及一篇关于从非技术人员那里学到的工程经验的精彩文章。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

另外，快速提醒一下，我最近推出了[一个教练项目](https://zendev.com/coaching.html)，为那些想要更多一对一帮助的人服务。我很高兴在你的技术技能上工作，但也像沟通，职业发展和管理压倒性的东西。如果你认为你可能感兴趣，[预订一个免费的介绍电话](http://bit.ly/kb-coaching-intro) l，让我们来谈谈吧！

#### CSS&SCS

##### [CSS:空选择器](https://dev.to/samanthaming/css-empty-selector-b6f)

一个简单但未被充分利用的伪选择器，`:empty`选择器可以在许多方面让你的生活变得更容易。这是一本非常简单的读物，首先解释了选择器是如何工作的，然后强调了它可以为您解决的一些常见的 UI 问题。

##### [交错 CSS 转场](https://css-tricks.com/staggered-css-transitions/)

多么有趣的小效果！使用`transition-delay`属性创建交错的 CSS 动画。爱死了！

##### [CSS 单位说明](https://alligator.io/css/css-units-explained/)

CSS 单位的固体概述。这里有比我意识到的更多的东西！例如，您知道`mm`(毫米)和`in`(英寸)是有效的 CSS 单位吗？对 web 来说没什么意义，但是当你考虑打印样式表的时候……非常有趣。另一对我不知道的是`vmin`和`vmax`，用于使用最小或最大尺寸的视口。这里有一些非常有趣的潜力…

##### [处理 SASS 中未使用的 CSS 以提高性能](https://www.smashingmagazine.com/2019/08/handling-unused-css-sass-performance/)

警告:这是一篇很长的文章，有很多东西需要你去思考。对于既定的目标(从生成的电子表格中删除未使用的 CSS ),像 uncss 这样的工具不仅更简单，而且可能更好。然而。这是一个关于元编程和使用 SCSS 动态包含不同 CSS 文件的迷人探索，让我思考在构建框架、库和工具包时使用这种方法的不同方式。值得通读，如果这种类型的事情是你的果酱。

##### [关于 CSS-in-JS 的不同观点](https://css-tricks.com/the-differing-perspectives-on-css-in-js/?ref=heydesigner)

Chris Coyier 汇集了该领域许多专家的各种观点，开始为 CSS-in-JS 何时有意义、何时没有意义绘制更多的路线图。这是一个令人担忧的话题，他在引入一系列观点方面做得很好。

#### JavaScript

##### [JavaScript 中的一流函数](https://nick.scialli.me/first-class-functions-in-javascript/)

对我来说，第一类函数(可以作为参数传递给其他函数的函数)是函数式编程中最强大的概念之一，也是提高开发人员技能的重要因素。几乎每一种现代语言中都包含了它们，JavaScript 让它们变得非常简单易用。这篇文章很好地解释了这个概念，并展示了它们如何有用的例子。

##### [JavaScript Promise 组合子:。all()，。race()，。allSettled()](https://2ality.com/2019/08/promise-combinators.html)

这是一个很好的例子，看看你可以用不同的方式来组合承诺。探究它们背后的思想，查看实现，并强调用途。这是一本提升你的承诺和异步工作技能的好书。

##### [es 2019 所有新的提示和技巧](https://css-tricks.com/all-the-new-es2019-tips-and-tricks/)

JavaScript 语言最新特性的更新。当然，你可能已经通过 babel 使用了这些，但是它们现在是官方的，这意味着浏览器支持变得越来越好。如果您使用 polyfill.io 这样的服务，您可以只将 polyfill 发送到需要它们的浏览器，并利用较小的包大小和高度优化的浏览器实现(如果存在)。

##### [现代反应测试，第 1 部分:最佳实践](https://blog.sapegin.me/all/react-testing-1-best-practices/)

我喜欢这篇文章(以及本系列的下一篇文章[这里](https://blog.sapegin.me/all/react-testing-2-jest-and-enzyme/))，因为它们不仅深入探讨了“如何”测试，还以特定的方式探讨了“为什么”测试，并探索了测试 ui 与测试后端服务不同的一些方式，因此可能有不同的东西需要优化。

##### [充分利用 Vue 异步组件](https://logaretm.com/blog/getting-more-out-of-vue-async-components/)

这是一个有趣的练习，研究了一系列利用异步加载来根据需要拆分和加载代码的方法。通常这种对话会从异步组件开始和停止，但是它们会比这更深入。

#### 其他牛气

##### [【原生懒人】为网页加载](https://web.dev/native-lazy-loading)

我对此超级兴奋！通过一个简单的 HTML 属性为图像、iframes 等提供浏览器级的 lazyloading。目前只在 Chrome 中发布，但深入研究 whatwg 问题 Webkit 团队有很大的参与，所以我想 Safari 不会落后太多，至少会进入大多数移动浏览器，这是最有价值的。

##### [第一个字节的时间:它是什么，为什么重要](https://csswizardry.com/2019/08/time-to-first-byte-what-it-is-and-why-it-matters/)

有趣的是，我还没有看到前端开发人员讨论过性能的一个方面——甚至一个字节返回到客户端浏览器之前的纯延迟。着眼于所有可能导致这种情况的因素，并挖掘一些新的可用工具来开始打破这种局面并加以改进。

##### [用 Cloudflare Workers 构建全栈无服务器应用](https://css-tricks.com/building-a-full-stack-serverless-application-with-cloudflare-workers/)

这是一次阅读之旅…作者构建了一个从代码上看起来像相对传统的节点服务器应用程序的东西。它有运行渲染 HTML 等功能的路径，但尽管相对熟悉，它将使用他们的“工人”无服务器平台在 Cloudflare 的全球分布式 CDN 上运行。给你一个 100%无服务器的“传统”外观的应用程序，我希望运行起来快如闪电，而且非常便宜。你所做的就是写 JavaScript。介意。吹了。

##### [我从非技术人员那里偷来的所有最好的工程建议](https://medium.com/@bellmar/all-the-best-engineering-advice-i-stole-from-non-technical-people-eb7f90ca2f5f)

这是一篇付费的中篇文章(这是我通常在这篇时事通讯中尽量避免的)，但是如果你只是在看 1，他们应该会让你通过，而且它太好了，不能跳过。难以置信的洞察力，可以应用于工程，解决问题，或只是一般的生活。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
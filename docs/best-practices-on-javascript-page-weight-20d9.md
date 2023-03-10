# JavaScript 页面权重的最佳实践

> 原文：<https://dev.to/trackjs/best-practices-on-javascript-page-weight-20d9>

网站继续膨胀，有过大的图像、自定义字体和太多的 JavaScript。许多网站上大量性能不佳的 JavaScript 拖了桌面浏览器的后腿，压垮了低端移动设备。没有人想在那样的网站上工作。

为了让你的网站更好、更快、更轻便，我们收集了一些高级的最佳实践来减少你的 JavaScript 负载。

## 少用 JavaScript。

如果你想让你的页面花更少的时间下载和执行 JavaScript，那就少用 JavaScript 吧！这并不像你想象的那么明显。

当面临一个需要解决的问题时，许多 JavaScript 开发人员会使用一个库或框架插件。但是通常，这些库解决的问题比原来的问题要多得多，并且需要更多的代码来完成。经过几个开发周期后，您会发现自己有几十个库，每个库都需要 10-50kb 的 JavaScript 来完成它们的工作。

相反，考虑探索一下这个库是如何解决你的问题的。也许您可以将相关的代码和测试提取到您自己的项目中，只关注您需要的部分。

更好的是，[也许你根本不需要 JavaScript](http://youmightnotneedjs.com/) ！有许多常见的 web 模式可以使用 HTML 和 CSS 的原生浏览器功能来实现。例如，查看 [TrackJS 文档](https://docs.trackjs.com/)上的移动抽屉菜单。都是 CSS，不需要 JavaScript。

[![Drawer menu with no JavaScript](img/4e1f6b00d4eeaa4c4e2598312268450e.png)](///asseimg/blog/2019-09-best-practices-javascript-page-weight/docs-drawer.png)

有时，您可能需要**拉一个 JavaScript 库来使用外部服务，比如邮件程序、交易处理器或分析日志。你应该总是对你添加到页面中的 JavaScript 持批评态度。确保您理解:**

*   库的总大小。不仅仅是初始脚本，而是所有可能被异步加载的资产。一个 5kb 的脚本可以在以后加载 100kb 的其他文件。

*   总的大小适合你得到的吗？如果您的日志库超过 10kb，您可以找到一个更好的客户端日志记录器，它不会使您的页面膨胀。

*   图书馆什么时候改变？如果你正在加载一个通用端点，比如`example.com/library.js`，你已经给了一个外部团队按照他们的时间表中断你的页面*的能力。检查你是否能[加载一个版本化的 URL](https://github.com/TrackJs/trackjs-package/blob/master/CHANGELOG.md) ，或者更好的是，自己托管这个库。*

[![Tricky JavaScript library loading extra files](img/7358d5d5afa87ad9b7431fcffe81d0f4.png)](///asseimg/blog/2019-09-best-practices-javascript-page-weight/fat-js-library.png)

## 在你需要的时候，加载你需要的东西。

你可能不需要在每一页上都使用所有的 JavaScript。有些 JavaScript 是针对特定页面的，有些只针对登录用户。通过只下载你需要的脚本，你的页面对每个人来说都会更轻更快。

这可以像定义多个入口点脚本一样简单。比如一个`main.js`到处加载，而`blog.js`只加载在博文页面。`main.js`将包含像分析这样的一般行为，而`blog.js`可以包含像评论和分享这样的特殊行为。

如果你使用的是 Webpack 这样的脚本捆绑器，可以选择[将你的脚本分解成独立的捆绑包](https://webpack.js.org/guides/code-splitting/)并在需要的时候异步加载。

不管你如何划分你的 JavaScript 资产，考虑异步加载它而不是阻塞。异步加载的脚本在下载和执行时不会妨碍用户，允许用户更早地开始理解您的内容并与之交互。

## 下载 JavaScript 更快。

一旦你减少了 JavaScript，只关注你需要的部分，你仍然需要下载并执行它。所以让我们尽快完成。让它更快的最好方法是将你所有的 JavaScript 组合成几个为交付而优化的包。这可以通过像 Webpack、Parcel、Google Closure Compiler 这样的工具，或者只是将脚本连接在一起。

如果你不能把你的 JavaScript 捆绑在一起，或者你有不止一个捆绑包，你可以考虑在你的 web 服务器上启用 HTTP/2。HTTP/2 允许多个文件在同一个连接上流式传输，减少了下载脚本的开销。

如果您从外部服务加载脚本，请检查它们是否提供了可以从 npm 下载和捆绑的 [JavaScript 库。减少对服务 CDN 的外部请求将节省大量查找和与新连接握手的开销。](https://www.npmjs.com/package/trackjs)

* * *

有了这些 JavaScript，很多事情都会出错。TrackJS 的前端错误监控和崩溃报告会告诉你什么时候出现问题，以及如何修复。此外，我们的代理只有 8kb，托管在一个版本化的端点上，并通过 npm 分发，因此您可以将其与您的脚本捆绑在一起。我们使您的网站可靠并保持快速运行。
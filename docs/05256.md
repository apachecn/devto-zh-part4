# 星期五前端:两年运行版

> 原文：<https://dev.to/kball/friday-frontend-two-years-running-edition-24e3>

这是我们的第 105 版星期五前端！这意味着我们已经连续两年多了。非常感谢您继续关注我们的节目，如果您还想看更多或更少的内容，请告诉我。

这个星期我们有各种各样的东西。我喜欢关于用 Sass 创建图标系统的文章，在 Array.from 的文章中找到了更好的方法，并且对 Vue 社区指南感到非常兴奋。你最喜欢哪些文章？让我知道，这样我可以继续找到你想要的东西。

最佳，

来自 ZenDev 的 KBall

另外，快速提醒一下，我最近推出了[一个教练项目](https://zendev.com/coaching.html)，为那些想要更多一对一帮助的人服务。如果你感到不知所措，或者不知道该把注意力集中在哪里，这可能适合你。[预订一个免费的介绍电话](http://bit.ly/kb-coaching-intro)，让我们来谈谈吧！

#### CSS&SCS

##### [用 Sass 创建可维护的图标系统](https://css-tricks.com/creating-a-maintainable-icon-system-with-sass/)

这是一个关于如何构建一个完全封装在一个 CSS 文件中的自定义图标系统的精彩展示。比许多 CSS/SCSS 示例更强烈地使用 Sass 特性，所以这是学习更多 Sass 的好资源。

##### [可以在 CSS 中旋转光标吗？](https://css-tricks.com/can-you-rotate-the-cursor-in-css/)

快速击中。答案是肯定的……差不多。您可以在 CSS 中旋转光标，但前提是您实际上用页面中的元素替换了光标本身。有意思。

##### [CSS 命名颜色从何而来？](https://www.chenhuijing.com/blog/where-did-css-named-colours-come-from/#%F0%9F%91%9F)

有趣，可爱…完全免费。我想。我是说，谁在乎东西是从哪里来的呢？(这是一个笑话——我实际上认为了解我们行业的历史是超级重要的)。许多我们认为经过深思熟虑的事情往往是完全随意的…比如 CSS 中的颜色可以追溯到 20 世纪 30 年代的随机油漆公司。

##### [CSS 可以做到这一点...而且很恐怖！](https://www.aaron-powell.com/posts/2019-08-14-css-can-do-this-and-its-terrifying)

前端社区中争论的一个常见来源是关于“CSS 是否是一种完整的编程语言”。我坚定地站在“是”的阵营，尽管我喜欢强调 CSS 是一种不同于我们习惯的语言类型，因为它处理的是一个不同的领域。原来 CSS 是图灵完备的，这意味着如果你足够复杂，你实际上可以做任何事情…坏处呢？它可以用来做各种各样的恶作剧。就像本文中详细描述的那样。

##### [纯 CSS:外星人通勤上班路上睡着](https://codepen.io/perbyhring/full/jpQwav)

归类到灵感桶中(内容警告:有很多动作，如果你很敏感，跳过这个)。一个 codepen 展示了一个完全用 CSS 制作的超级酷的动画。绝对值得一试，以了解事情是如何运作的。

#### JavaScript

##### [JavaScript array . from()](https://dmitripavlutin.com/javascript-array-from-applications/)5 个好用的应用

我不知道怎么回事，但不知何故我完全错过了`Array.from`功能。我不知道这个存在！但是对于一些突出显示的用例(数组初始化和将类似数组的对象转换为数组)，这比我当前的解决方案好得多。爱死了！

##### [如今在生产中使用原生 JavaScript 模块](https://philipwalton.com/articles/using-native-javascript-modules-in-production-today/)

原生模块支持承诺对今天的捆绑生态系统进行一些非常有趣的改进，无论是在易用性方面，还是在改进的可缓存性和发送到浏览器的少量代码方面。这个话题出现在最近关于 JS 工具的 JSParty 插曲中，关注的是如何过渡到今天的原生模块。这篇文章解决了这个问题。超级酷的东西！

##### [使用 JavaScript 生成器函数降低内存使用率](https://dev.to/treyhuffine/use-javascript-generator-functions-to-reduce-memory-utilization-26be-temp-slug-9971242)

生成器是 JavaScript 中相对较新的特性之一，我还没有开始使用它，但是我越来越多地听说了它们，本文强调了使用它们的一个好处:与 node 中的流非常相似，您可以使用相对最少的内存处理大量数据。

##### [无逻辑 JSX](https://verekia.com/react/logic-less-jsx/)

这是一篇关于如何让 JSX 更具可读性的精彩文章。JSX 是构建动态组件的一个超级强大的工具，但是这种能力经常导致组件过于复杂，难以理解和维护。这篇文章通过一系列实践来指导你实现可读的、可维护的 JSX。

##### [反应过来布局](https://react-layouts.com/)

一组通用布局的抓取并运行 React 实现。设置 rebass、主题 ui 和情感的版本。我不得不承认，我仍然害怕看到这样的 CSS-in-JS，但是在这里进行复制/粘贴的方便性绝对是好的。

#### 其他牛气

##### [年中 ADDA Web & App 无障碍诉讼报告](https://blog.usablenet.com/midyear-ada-web-accessibility-lawsuit-report-blog)

这里有些疯狂的数据。大约 48%的美国 500 强零售商因为网站缺乏可访问性而被起诉。每小时都有与无障碍相关的诉讼发生。哇哦。如果你需要弹药来证明花时间让你的应用程序变得可访问的重要性，这份报告充满了弹药！

##### [AMP-script:AMP<3js](https://blog.amp.dev/2019/08/21/amp-script-amp-heart-js/)

尽管 amp 很丑，但它似乎仍在继续发展，amp-script 之类的东西允许你在 AMP 页面上使用更通用的 JavaScript，这听起来像是在改善体验方面迈出的一大步。

##### [Vue 社区指南](https://vue-community.org/)

我喜欢这个！我想我以前从没见过这样的东西。由社区撰写的关于开源项目的社区介绍和指南。因此，我们经常关注技术文档，只是随意链接到社区内容，期望新用户能够简单地发现它。这为在哪里以及如何找到围绕 Vue.js 的社区提供了非常周到的指导，我很乐意看到其他大型项目采用这种方法。还有一个伴随的[介绍帖](https://dev.to/dobromirhristov/the-developer-s-guide-to-the-vue-ecosystem-4amb)。

##### [为什么我的反应有问题](https://www.sonniesedge.net/posts/react/)

另一方面，上周 React 社区发生了很多混乱。这篇文章在讨论的某一方面非常固执己见，但我是出于这个原因特意发表的:作为开发人员，我们有过度关注代码并试图跳过人类问题的倾向。这是一个问题。我们有责任思考和讨论在我们的社区中什么是可接受的行为，并倾听那些在这些社区中感到威胁和被排斥的人。

##### [通过嵌入式图像预览加快图像加载速度](https://www.smashingmagazine.com/2019/08/faster-image-loading-embedded-previews/)

非常有趣的是，有一种技术将渐进式图像与一些 JavaScript 技巧结合起来，在获取所有图像之前，先在整个页面中加载 lowfi/preview 图像。我很想看到有人把这个想法包装成一个易于使用的库！:)

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
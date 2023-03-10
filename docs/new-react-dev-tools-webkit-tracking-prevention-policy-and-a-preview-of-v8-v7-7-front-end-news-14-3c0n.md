# 新的 React 开发工具、WebKit 跟踪预防策略和 V8 v7.7 预览版|前端新闻#14

> 原文：<https://dev.to/adriansandu/new-react-dev-tools-webkit-tracking-prevention-policy-and-a-preview-of-v8-v7-7-front-end-news-14-3c0n>

<figure>[![](img/5c24c828a1c3153f939182c07a02fd20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9pehDMJ---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AFJLRRF_E065sKuTH4nPI2g.png) 

<figcaption>在 YouTube 上查看[https://youtu.be/NW5JXabA0UM](https://youtu.be/NW5JXabA0UM)</figcaption>

</figure>

大家好，欢迎收看新一轮的前端新闻。今天我有以下几个主要话题:

*   首先，我们来看看新的 React 开发工具
*   WebKit 发布他们的跟踪预防策略
*   我们得到了 V8 JavaScript 引擎的 7.7 版本预览

我也有一些软件更新要提，还有一个本周的全新开发者。所以请陪我到最后，找出所有的细节。

一如既往，我也为那些喜欢听而不是读的人准备了视频版本。如果你喜欢这种格式，如果你也能订阅 YouTube 频道，我将不胜感激。

### 介绍新的 React DevTools

不管你喜不喜欢，React 仍然是最广泛使用的构建应用和网站的库之一。开始使用它的一个原因是他们出色的 Chrome、Firefox 和(Chromium) Edge 开发工具扩展。

React 开发人员工具刚刚进行了一次重大更新，版本 4 现在可供每个人使用。与以前的版本相比，这次更新在性能上有了显著的提高。有一整篇文章致力于使这成为可能的改变，你可以从下面的链接部分获得它。

回到开发者工具，新版本也为用户体验带来了多项更新。有新的过滤器允许更精确的组件树视图，对钩子的支持得到了改进，搜索体验变得更加流畅，等等。

分析器的功能也得到了增强。开发人员现在可以使用`reload and profile`选项测量初始负载的性能，并且现在可以导出分析器数据并与其他开发人员共享。更多信息可以在官方发行说明中找到。

如果您已经在使用 React 开发人员工具，那么它应该会自动接收更新。独立 shell 的用户可以从 NPM 安装新版本。所以，走出去，开始使用新的工具。

编码快乐！

*   [https://reactjs.org/blog/2019/08/15/new-react-devtools.html](https://reactjs.org/blog/2019/08/15/new-react-devtools.html)
*   [https://github . com/Facebook/react/blob/master/packages/react-devtools/changelog . MD # 400-August-15-2019](https://github.com/facebook/react/blob/master/packages/react-devtools/CHANGELOG.md#400-august-15-2019)
*   [https://github . com/Facebook/react/blob/master/packages/react-dev tools/overview . MD](https://github.com/facebook/react/blob/master/packages/react-devtools/OVERVIEW.md)

### WebKit 发布其跟踪预防策略

WebKit 发布了一项政策，描述了他们计划在默认情况下直接从浏览器中阻止的 web 跟踪做法。该文档受 Mozilla 跟踪政策的启发，指定了使用的定义、可能的跟踪类型以及默认情况下将阻止的类型。

有一些合法的功能和实践使用的技术也可以用于跟踪，如欺诈预防，机器人检测和受众测量，仅举几例。WebKit 旨在优先考虑用户利益，而不是保留现有的实践。他们保留在未来更新政策的权利，唯一的目的是创造更好、更私密的用户体验。

欲知详情，请点击下面的链接。

*   [https://webkit.org/tracking-prevention-policy/](https://webkit.org/tracking-prevention-policy/)

### V8 发布 v7.7

谷歌开发人员正在发布他们的 V8 JavaScript 引擎的新版本，以及 Chrome 浏览器的每个主要迭代。该团队刚刚公布了即将到来的 7.7 版本变化的预览，该版本将在几周内与 Chrome 77 一起发布。

亮点在于通过惰性反馈分配、可伸缩的 WebAssembly 后台编译和堆栈跟踪改进来提高性能。还将有对`Intl.NumberFormat` API 的更新，将增加紧凑符号、科学符号、工程符号、符号显示和测量单位。

更多的细节可以在官方博客上找到。

*   [https://v8.dev/blog/v8-release-77](https://v8.dev/blog/v8-release-77)

### 软件更新和发布

**创建 React App 3.1.0**

打开“软件更新”部分，是 Create React App 3 . 1 . 0 版本的发布。

Create React App 是官方支持的创建单页 React 应用程序的方式。它提供了一个没有配置的现代构建设置。更新 3.1.0 增加了对 ESLint 6 的支持，以及其他一些小的错误修复和文档更新。

*   [https://github . com/Facebook/create-react-app/releases/tag/v 3 . 1 . 0](https://github.com/facebook/create-react-app/releases/tag/v3.1.0)

**包裹 2 . 0 . 0-α1**

Parcel 是一个速度惊人的零配置 web 应用程序捆绑器。我们将在 2.0.0 版本的全新 alpha.1 版本中获得预览。这是对应用程序的完全重写，重点放在可扩展性、可伸缩性和可靠性上。

所有细节都可以在下面链接的博文公告中找到。

*   [https://medium . com/@ devongovett/parcel-2-0-0-阿尔法-1-is-here-8b160c6e3f7e](https://medium.com/@devongovett/parcel-2-0-0-alpha-1-is-here-8b160c6e3f7e)

### 一周的开发者

<figure>[![](img/cb414862c456c145813c9ef72476b7c8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fzulCmBv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3Q6ZJataldppIh6MwjlwjQ.png) 

<figcaption>源:[https://wesbos.com/](https://wesbos.com/)</figcaption>

T9</figure>

这一集，本周开发者的头衔将授予独一无二的韦斯·博斯。他是一个名副其实的独角兽，因为他对设计、开发、商业和教学充满热情，而且他毫不费力地完成了所有这些工作。他在世界各地的活动、会议和培训研讨会上写作、在 YouTube 上制作视频、发推特和演讲。

最重要的是，韦斯是一位教育大师——他是 HackerYou 的首席讲师，他有很多关于网络技术的课程。Flexbox、CSS Grid、ES6、Node、React、Redux……Wes 有针对所有这些以及更多的课程。他的付费课程对世界上的每个人来说都是负担得起的，因为他是平价购买力的坚定支持者，他为来自全价价格偏贵的国家的用户提供大折扣。

从宣布的那一刻起，我就想上他的 CSS 网格课程，但我从来没有……为拖延欢呼三声。

[![](img/13185aa42e701b9a5aab358da4b9662a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I6L7I2qX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/460/1%2Aq8HeGUjIM1yIhC03GM6wbA.jpeg)

韦斯与妻子和两个女儿住在加拿大汉密尔顿。他对烹饪充满热情，他的猪肉拉面让人爱不释手，他可能会在任何萤火虫会议上扮演一个非常可信的角色扮演。

<figure>[![](img/40582bb8280759dc694fab7af889d181.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c0MTdQay--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/500/1%2AyTodYBzISS9qA8P3Ve9vBw.gif) 

<figcaption>“我是风中的一叶。看我怎么翱翔！”</figcaption>

</figure>

*   [https://wesbos.com/](https://wesbos.com/)
*   [https://twitter.com/wesbos](https://twitter.com/wesbos)
*   [https://www.youtube.com/channel/UCoebwHSTvwalADTJhps0emA](https://www.youtube.com/channel/UCoebwHSTvwalADTJhps0emA)

### 今日问题

你完成韦斯·博斯的课程了吗？如果有，是哪一个？请在评论区或在[https://twitter.com/frontendnexus.](https://twitter.com/frontendnexus.)上留下你的答案

这一版就这么多了。在 https://twitter.com/frontendnexus[的 Twitter 上关注 Front End Nexus](https://twitter.com/frontendnexus)，一旦有新的更新，我们将立即通知您。我还想鼓励你在 https://www.youtube.com/channel/UCgACtqiDmnSaskDIBsK54ww[的](https://www.youtube.com/channel/UCgACtqiDmnSaskDIBsK54ww)订阅 YouTube 频道。一旦该频道达到 100 名订户，我可以解锁更多的选项，因此非常感谢您的支持。

祝您度过愉快而富有成效的一周，我们下次再见！

* * *
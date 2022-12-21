# 星期五前端:来自巴塞罗那版

> 原文：<https://dev.to/kball/friday-frontend-coming-from-barcelona-edition-5f8d>

希望你度过了愉快的一周！这个星期我在巴塞罗那旅行，被这个令人惊叹的城市所震撼。如果你有机会的话，这绝对是一个值得参观的好地方。

本周的时事通讯有很多很棒的教育内容，但本周让我印象深刻的是那些尖刻的帖子。首先是一个娱乐性(和教育性)的 JavaScript 迷因，然后是一个关于 JavaScript 依赖性问题的幽默。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

又及:我会再旅行一周。通讯稿将于下周正常发布，但如果你给我写信，我可能需要一段时间才能回复你。

#### CSS&SCS

##### [@规则有多少特异性，像@关键帧和@媒体？](https://css-tricks.com/how-much-specificity-do-rules-have-like-keyframes-and-media/)

有趣的问题，相对简单的答案，在`@keyframes`中有一个奇怪的小角落，看起来有问题，应该避免。

##### [多列操纵](https://every-layout.dev/blog/multi-column-manipulation/)

即使是多栏设计，多栏布局属性通常也不是您想要的。但是，有时候正是你需要的。这是我见过的对其工作原理的最深入的探究。

##### [如果需要深度浏览器支持，不要逗号分隔:焦点在内](https://css-tricks.com/dont-comma-separate-focus-within-if-you-need-deep-browser-support/)

使用新的`:focus-within`伪选择器突出了一个简单的问题。这一点实际上也适用于任何新的选择器..如果你用逗号组合选择符，并且其中一个不能被浏览器理解，所有其他的也将被忽略！

##### [柔性盒和衬垫](https://dev.to/huijing/flexbox-and-padding-2683)

使用 Flexbox 时一个非常常见的问题的解释和解决方法。如果有溢出，在 flex 父对象上放置填充并不像你期望的那样工作！阅读这篇文章，了解为什么以及如何修复它。

#### JavaScript

##### [React 钩子会取代高阶元件(hoc)吗？](https://medium.com/javascript-scene/do-react-hooks-replace-higher-order-components-hocs-7ae4a08b7b58)

替换 HOCs 绝对是我认为 React 钩子很酷的原因之一…至少在某些情况下，我们最终会在功能上用超级嵌套的组件树分层。然而，仍然可能存在 hoc 有价值的情况，本文深入探讨了利弊，以帮助您决定何时使用哪一种。

##### [为什么每个 Vue 开发者都应该为 Quasar 1.0 而激动](https://medium.com/quasar-framework/quasar-1-0-4bc696d60c1b)

一个专门针对 Vue.js 的新 UI 框架。吹捧的方面是内置的 treeshaking 和 build targets，包括桌面和移动，以及大量的 123 个内置组件和简单的可扩展性。使用手写笔，样式看起来可以适度控制(唉)。对于寻找简单 UI 工具包的 JS 开发人员来说，它肯定比寻找基于 UI 的工具包的开发人员更有意思。看看结果如何会很有趣。

##### [vue . js 中要避免的 3 个反模式](https://www.binarcode.com/blog/3-anti-patterns-to-avoid-in-vuejs/)

我喜欢这篇文章，不仅因为所识别的反模式是非常容易犯的错误(我认为我在开始时犯了这些错误，并且在代码审查中已经纠正了所有这些错误)，还因为它解释了为什么反模式会给你带来麻烦。

##### [JavaScript 新特性:Google I/O 2019 总结](https://itnext.io/whats-new-in-javascript-google-i-o-2019-summary-d16bd2308412)

一系列新 JavaScript 特性的概要。既有正文，又有解释同一系列的视频。我非常感谢作者不仅包括了这些特性的作用，还包括了它们在标准化过程中的位置&浏览器+节点支持图看起来是什么样子。

##### [身材苗条简介](https://dev.to/dceddia/introduction-to-svelte-587l-temp-slug-1205141)

我对我们在前端世界看到的预编译和预计算的趋势很感兴趣。其中一部分是静态站点生成器(SSG)和 JAMstack，还有 Gatsby 之类的东西。但是另一个很大的部分是像 Svelte(和我最近了解到的 [Stencil](https://stenciljs.com) )这样的框架，它们尽可能多地在编译时完成工作，依赖于原生浏览器 API，而不是像 React、Angular 或 Vue 一样提供大型运行时。这篇文章是对 Svelte 的一个很好的介绍，可以帮助你开始使用它。

##### [我见过的最好的 JavaScript Meme，详细讲解](https://www.freecodecamp.org/news/explaining-the-best-javascript-meme-i-have-ever-seen/)

一个有趣的类型转换，以及如果不使用' === '会给自己带来怎样的麻烦。

#### 提升链接

##### [宣布 TylerMcGinnis.com 的两个新课程——React 和 React Hooks](https://tylermcginnis.com/subscribe/?utm_source=Friday%20Frontend%20Newsletter&utm_medium=Email&utm_campaign=Jul_2019_launch)

TylerMcGinnis.com 最初的 React 课程于 2016 年推出，自那以来已有超过 8 万名学生参加，平均评分为 4.8/5。今天，他们很高兴推出它的继任者。他们全新打造的 React 和 React Hooks 课程将在 2019 年教你所有你需要知道的关于编写 React 的知识。[学习 React 钩子](https://tylermcginnis.com/subscribe/?utm_source=Friday%20Frontend%20Newsletter&utm_medium=Email&utm_campaign=Jul_2019_launch)

#### 其他牛气

##### [数据表用于建议值而不强制值](https://css-tricks.com/datalist-is-for-suggesting-values-without-enforcing-values/)

datalist 元素是我从来没有真正使用过的东西，但是它在上个月出现过几次，看起来非常有用，尤其是在尝试将您的站点置于低 JavaScript 饮食时。

##### [“没有办法阻止这种情况”，表示只有开发社区才会定期发生这种情况](https://medium.com/@nimelrian/no-way-to-prevent-this-says-only-development-community-where-this-regularly-happens-8ef59e6836de)

刻薄，但是这个讨论很重要——为什么在 JavaScript 生态系统中有如此普遍的包管理问题？我们能做些什么呢？我不认为在 snark 之后提供的“严肃的”要点能找到问题的根源。

##### [我用 50 MB 的预算上网一天](https://www.smashingmagazine.com/2019/07/web-on-50mb-budget/)

这是对“大小疏忽”，也就是没有密切关注你的图像、javascript 等的大小，如何影响世界上数据传输缓慢且昂贵的地方的人们的有趣观察。包含大量优秀的性能技巧，但也只是对世界各地数据不平等的有趣观察。

##### [前端开发者订阅的最佳时事通讯](https://dev.to/remotesynth/the-best-newsletters-to-subscribe-to-for-a-frontend-developer-1ni1)

虽然明显不完整，因为它缺少这份通讯😜，如果你想订阅更多的时事通讯，这是一个可靠的列表。事实上，我订阅了其中相当一部分。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
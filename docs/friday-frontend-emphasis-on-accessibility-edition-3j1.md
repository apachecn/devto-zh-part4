# 星期五前端:强调可访问性版本

> 原文：<https://dev.to/kball/friday-frontend-emphasis-on-accessibility-edition-3j1>

六月过半快乐！本周，我们有许多关于可访问性的优秀文章——既有哲学层面的，也有使用或避免的具体策略。我特别喜欢关于网页可访问性的文章。

还有一大堆其他的好东西……CSS 心态的帖子是一个很好的帖子，特别是对于 CSS 新手的开发人员，JavaScript Transducers 的文章完全让我震惊，而一个受 hooks 启发的 API 的 Vue RFC 让我对 webdev 的未来感到前所未有的兴奋。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

又及:下周我将在 NodeConf Columbia 主持一个小组讨论会——如果你碰巧参加了会议，给我发个短信，我们一起喝杯咖啡吧！

#### CSS&SCS

##### [CSS 心态](https://mxb.dev/blog/the-css-mindset/)

许多新开发人员面临的最大挑战之一是 CSS 采用了与更“传统”的编程语言非常不同的思维模式。CSS 是声明性的、容错的，并且是为具有许多未知变量(内容长度/大小/格式等)的环境而设计的。因此，它背后的思想和最佳实践与 JavaScript 之类的东西非常不同。也就是说，肯定有一组更大的概念可以掌握，本文很好地列出了其中的一些。

##### [网格、内容重新排序和可访问性](https://rachelandrew.co.uk/archives/2019/06/04/grid-content-re-ordering-and-accessibility/)

随着 CSS Grid 和 Flexbox 在页面上的排序和移动变得更加容易，辅助技术读取这些内容的工具却没有跟上。现在创建一个漂亮的布局是非常容易的，它是完全不连贯的，可以在屏幕阅读器中使用。这是一篇非常重要的文章，它既警告我们要注意什么，又呼吁在工具层面上进行改进。

##### [用高级 CSS 条形图构建静态投资组合](https://webdesign.tutsplus.com/tutorials/build-a-static-portfolio-with-advanced-css-bar-chart--cms-33050)

一步一步的用 CSS 构建条形图的教程。涵盖了很多主题——flexbox、自定义属性、转换、过渡等等。如果教程是你喜欢的学习方式，这种方式有很多优点。

##### [前 20 名 CSS 按钮(+动画)](https://dev.to/webdeasy/top-20-css-buttons-animations-f41)

另一方面，如果你喜欢通过例子学习(一个接一个的例子)，那么这可能更适合你。大量的 20 个不同的 CSS 按钮的例子和一些非常酷的动画，都在他们自己的代码中，所以你可以玩和修补(或者直接滑动)任何你喜欢的。

#### JavaScript

##### [神奇、神秘的 JavaScript 转换器](https://jrsinclair.com/articles/2019/magical-mystical-js-transducers/)

这是一本关于传感器如何工作的精彩读物，使用 JavaScript 从 reducers 开始构建传感器，然后展示如何使用 Ramda 的内置传感器。如果这一切听起来像天书，你并不孤单-但读这篇文章，它会开始有意义。:)这是一种超级强大的方法，让您可以组合简单的函数来创建强大而高效的数据处理管道。

##### [升一级。分类游戏](https://css-tricks.com/level-up-your-sort-game/)

关于如何在一系列不同的场景中使用内置 Array.sort 方法的逐步教程，逐渐变得更加复杂。JavaScript 基础知识的坚实补充。

##### [JavaScript 中关于日期你需要知道的一切](https://css-tricks.com/everything-you-need-to-know-about-date-in-javascript/)

JavaScript 中的 OMG 日期真让人头疼。我一直在使用 moment.js 来使它变得简单，但是 moment 是一个令人惊讶的沉重的库(52K 缩小版，甚至没有区域设置)。如果你不想要那样的重量，掌握处理原生日期可能是值得的，如果你想这样做…这篇文章是我所见过的最全面的。

##### [8 个有用的 JavaScript 窍门](https://devinduct.com/blogpost/26/8-useful-javascript-tricks)

相当酷的技巧列表！我特别喜欢 spread & destructuring 的一些技巧。使用析构将一个对象拆分成两个对象的例子非常简洁。

##### [Vue RFC:改为通过基于函数的 API 公开逻辑相关的组件选项。](https://github.com/vuejs/rfcs/blob/function-apis/active-rfcs/0000-function-api.md)

这是一本引人入胜的读物。这是一个 RFC，针对 Vue 的一组新的 API，灵感来自 React 的钩子，但利用 Vue 的反应模型来创建一些我们更“像 Vue”的东西。这里跨主要 JavaScript 框架的思想和改进的交叉传播看起来真的很神奇，通读这篇文章让我对前端开发的未来感到非常兴奋。

#### 其他牛气

##### [让我们谈谈测试:关于测试哲学的 4 堂快速课](https://dev.to/kball/let-s-talk-testing-4-quick-lessons-on-the-philosophy-of-testing-3a0j)

(偏见提醒:这是我写的)像[摩卡](https://mochajs.org/)、[茉莉](https://jasmine.github.io/)和 [Jest](https://jestjs.io/) 这样的工具已经让写作测试变得容易多了……但是仍然有差距。很难找到关于测试哲学的信息。测试什么，为什么。多少才够？我应该写什么类型的测试，它什么时候适合我的过程？本文从最近 JSParty 的一次对话中总结了一些关于测试哲学的重要经验。

##### [微前端](https://martinfowler.com/articles/micro-frontends.html)

微服务架构的前端等价物是什么？当然是微前端架构。这种方法很有意义，尽管在我看来，你肯定希望有一个内部组件库和一些跨前端的协调，这样你的 UI 就不会退化成一系列互不关联的体验。

##### [语境中的网络无障碍](https://www.smashingmagazine.com/2019/06/web-accessibility-context/)

这是一篇引人入胜的阅读，既强调了可访问性和辅助技术的历史背景，也深入探讨了我们今天在 web 上实现可访问性的方式。我强烈推荐通读这篇文章。

##### [指示焦点提高可达性](https://hacks.mozilla.org/2019/06/indicating-focus-to-improve-accessibility/)

查看焦点样式的重要性，讨论如何设计焦点样式(看在上帝的份上，不要完全删除它们，但你不是唯一一个发现默认焦点环很丑的人)，以及讨论管理焦点样式的未来改进。

##### [剖析无障碍形式:占位符的问题](https://www.deque.com/blog/accessible-forms-the-problem-with-placeholders/)

查看占位符的一些可访问性(和一般可用性)问题。我认为这是一个很好的例子，说明好的易访问性实践通常对所有人来说都是好的可用性实践。虽然强调的问题对那些有挑战的人影响最大，但我发现每一个替代方案对我来说都是可用性的提高。

##### [避免筋疲力尽成为一名网页开发者](https://dev.to/iam_timsmith/avoiding-burn-out-as-a-web-developer-ah2)

压倒性是我今天从你那里听到的关于 web 开发的最常见的挑战之一，压倒性常常会直接导致精疲力竭。但是还有其他因素，其中很多因素导致了 web 开发人员的高度倦怠。本文将讨论其中的一些因素，如何预见它们的到来，以及一些避免精疲力尽的策略。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
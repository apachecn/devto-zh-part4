# 星期五前端:WASM 的未来是可怕的版本

> 原文：<https://dev.to/kball/friday-frontend-the-future-of-wasm-is-awesome-edition-42m6>

希望你这一周过得愉快！本周有很多很棒的东西，但让我震惊的是对 WebAssembly 未来的展望，它包含在其他 awesome 的顶部。OMG 如果我们认为现在编程是好的，那么再等几年直到它真正开始运行！

我也喜欢 CSS 复选框的例子，JavaScript 析构用例的列表，以及关于赤裸裸的承诺是 NSFW 的文章(不像听起来那么淫秽，别担心！)享受！

最佳，

来自 ZenDev 的 KBall

另外，快速提醒一下，我最近推出了[一个教练项目](https://zendev.com/coaching.html)，为那些想要更多一对一帮助的人服务。如果你感到不知所措，或者不知道该把注意力集中在哪里，这可能适合你。[预订一个免费的介绍电话](http://bit.ly/kb-coaching-intro)，让我们来谈谈吧！

#### CSS&SCS

##### [如何用 CSS 变量让响应性超级简单](https://dev.to/perborgen/how-to-make-responsiveness-super-simple-with-css-variables-1gnm)

仔细看看 CSS 自定义属性(也称为 CSS 变量)如何让您将响应整合到 CSS 中的一个位置。这是有价值的，原因有几个。显而易见的是，它使你的代码更易读、更简短，但我认为更重要的是，它迫使你更全面地考虑你的风格，而不是一次解决一个组件的响应性。

##### [2019 复选框样式—可访问、简单、仅 CSS](https://codepen.io/jensimmons/pen/KKPzxJa)

这是一个很好的例子，展示了如何以一种简单、可访问、完全利用 CSS(没有图像、JS 或额外元素)的方式创建一个样式化的复选框，并在旧浏览器中无缝后退。

##### [可变字体动画用 CSS 和拆分 JS](https://css-irl.info/variable-font-animation-with-css-and-splitting-js/)

哇，这完全让我大吃一惊。可变字体已经在我的考虑范围之内了，因为它们可以用更少的下载实现一系列的排版，但是我没有意识到用 CSS 控制字体的方式也可以通过 CSS 打开各种很酷的新动画。精彩！

##### [CSS 网格入门:解剖](https://www.viget.com/articles/getting-started-with-css-grid-part-1/)

对 CSS 网格基本“解剖”的介绍还不错。一步一步地介绍您开始使用 Grid 所需的基础知识，并简单介绍一下 devtool 支持。

#### JavaScript

##### [我为什么喜欢 JavaScript 随意链接](https://dmitripavlutin.com/javascript-optional-chaining/)

深入探究一种新的 JavaScript 语言特性，这种特性刚刚进入标准化过程的第三阶段，即将出现在您身边的浏览器中。可选的链接允许更紧凑和可读的代码，特别是在处理深度嵌套的属性时。这篇文章深入探讨了这个用例，但是也触及了什么时候不要使用这个特性，这样你就不会陷入麻烦。爱死了！

##### [JavaScript 析构的 5 种有趣用法](https://dmitripavlutin.com/5-interesting-uses-javascript-destructuring/)

很好地展示了析构特性是如何为许多问题启用更简单的代码的。我经常使用数组项访问和不变性方面，但是其他一些用例看起来非常有趣。我喜欢动态属性析构使编写高级/可重用函数变得更容易的方式。

##### 

这是一篇关于在同步和异步代码之间搭桥时需要技巧的好文章。我知道承诺和异步是一个巨大的挫折和挑战的领域——如果你在那条船上，一定要看看这篇文章。对那些感兴趣的人来说，我最近看到了[这篇文章，它探讨了承诺、可观察到的事物和信息流之间的区别。](https://medium.com/javascript-in-plain-english/promise-vs-observable-vs-stream-165a310e886f)

##### [拖拽反应](https://css-tricks.com/draggin-and-droppin-in-react/)

我喜欢这篇文章的原因不是它展示了如何在 React 中实现拖放——正如文章本身所说，有许多库可以做到这一点。我喜欢的是，它展示了高阶组件(hoc)在钩子的新 React 世界中仍然非常有用的例子。在这种情况下，一个 HOC 为创建一个支持拖放的列表提供了一个超级简单和干净的界面，即使这些列表项的集合和顺序是使用钩子控制的。漂亮！

##### [介绍新的 React 开发工具](https://reactjs.org/blog/2019/08/15/new-react-devtools.html)

DevTools 完全支持钩子和更好的过滤能力来处理深度嵌套的组件层次结构…好吃！

#### 提升链接

##### [Udemy Legacy 特卖:顶级课程仅售 9.99 美元](https://click.linksynergy.com/fs-bin/click?id=hIdOL5Z4eK4&offerid=507388.10778&type=3&subid=0)

如果你准备好了下一门在线课程，现在是在 Udemy 购买的好时机。几乎每门课程都打折到 9.99 美元。我最近一直推荐的课程包括[完整的 JavaScript 课程 2019](http://bit.ly/zd-complete-javascript) 、 [Modern React with Redux](http://bit.ly/zd-modern-react) (我学习 React 的课程，更新了所有最新功能)，以及 [Vue JS 2 -完整指南](http://bit.ly/zd-vue2-complete)(由我最喜欢的 Udemy 导师之一马克西米利安·施瓦兹米勒(Maximilian Schwarzüller)主讲。)

#### 其他牛气

##### [WebAssembly 接口类型:与所有东西互操作！](https://hacks.mozilla.org/2019/08/webassembly-interface-types/)

这是一篇又长又有点复杂的文章，但是如果你对编程的未来感兴趣，哦，这是一篇好文章。看看一个关于 WebAssembly 的新提议，它将使 WASM 和几乎任何语言/运行时之间的轻松通信成为可能。这将允许无缝地将代码从一种语言嵌入到另一种语言中……想想“本机模块”,除了您不再需要在用户的机器上重新编译它们，更不用说您可以在 web 上“免费”使用它们，并且您得到了内置的沙盒！哇！

##### [服务器上的 WebAssembly:系统调用如何工作](https://www.youtube.com/watch?v=G4l8RX0tA3E&feature=youtu.be)

这是一个视频，也在看 WebAssembly 的互操作性，但这次是为了在服务器上进行系统调用。它短小精悍，值得你去尝试。

##### [基本自定义控件需求](http://adrianroselli.com/2019/08/basic-custom-control-requirements.html)

我以前发表过一些文章，解释了为什么如果你更多地依赖内置的浏览器控件，那么构建易访问的网站会更容易。这是一个很好的清单，您可以在构建自定义控件的情况下遵循，以确保您解决了所有国际化和可访问性问题。

##### [盖茨比中的 MDX 简介](https://alligator.io/gatsbyjs/mdx-in-gatsby/)

你是否曾经在一个 markdown 文件中工作，并且希望你可以在其中嵌入一些 JSX 来创建一个动态的、交互式的作品？没有吗？我也没有。:P 但是随着静态站点生成器的出现，我希望这种交互组件与其他方式生成的文件混合在一起，本教程将教你如何使用 mdx 实现这一点。

##### [需要显示按钮的加载状态时](https://uxmovement.com/buttons/when-you-need-to-show-a-buttons-loading-state/)

很好地展示了在按钮中显示加载状态的各种不同选项。我也很欣赏作者解释了什么时候值得定义它，什么时候足够快，你可能不需要费心。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
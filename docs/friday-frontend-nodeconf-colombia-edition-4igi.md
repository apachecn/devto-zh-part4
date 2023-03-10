# 周五前端:NodeConf 哥伦比亚版

> 原文：<https://dev.to/kball/friday-frontend-nodeconf-colombia-edition-4igi>

本周的节目将从哥伦比亚的麦德林为您直播，我在那里参加并主持了哥伦比亚 NodeConf 的一个专题小组。拉丁美洲的 JavaScript 社区正在蓬勃发展，有机会探索这座城市真是太棒了。他们有着如此惊人的故事，在过去的 30 年里，他们从世界上最危险的城市变成了创新、旅游和文化的新星。如果你热爱旅行，你绝对应该把麦德林列入你的清单。

无论如何，在前端，本周我们有许多有趣的文章要探索——我推荐的最热门的文章是在 CSS 调查的数据中探索，并收听题为“JavaScript 是网络的二氧化碳”的播客。有这么多迷人的见解。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [用最少的 CSS 制作响应迅速的表格](https://bradleytaunt.com/2019/06/11/responsive-tables/)

通过各种方法使表格具有良好的响应性。我有点喜欢他们把小屏幕的显示模式改成 flexbox。那么您的表格数据对于屏幕阅读器&大屏幕用户来说仍然像一个表，但是您可以在移动设备上转移到一个更加移动友好的类似卡片的方法。

##### [我真的需要 CSS 模块吗？](https://davidwells.io/blog/do-i-really-need-css-modules)

一个固执己见的帖子，但是对 CSS 模块的好处/论点的一个很好的总结。并没有真正深入到技术中，而是专注于为采用提供案例，然后链接到资源。

##### [功能 CSS 的危险](https://dev.to/browserlondon/the-perils-of-functional-css-3cef)

函数式 CSS，原子 CSS，实用优先 CSS…不管你怎么称呼它，这种方法最近已经得到了很多关注。我见过有人试图顺风顺水，并大声宣称他们永远不会回去。本文对这一趋势进行了反击，强调了函数式 CSS 的一些挑战。

##### [CSS 状态 2019](https://2019.stateofcss.com/)

“CSS 状态”调查的第一版已经公布了它的数据。由做“JS 状态”调查的同一批人带给你的，不完全清楚他们的样本有多大代表性，但仍然有一些非常有趣的数据。

##### [DOM 元素尺寸和 CSS 变换](https://www.impressivewebs.com/dom-element-dimensions-and-css-transforms/)

这很有趣。有许多不同的方法可以使用 JavaScript 测量元素的尺寸——事实证明，这些不同的方法对 CSS 转换的反应是不同的。令人着迷。

#### JavaScript

##### [越减({...传播})反模式](https://www.richsnapp.com/blog/2019/06-09-reduce-spread-anti-pattern)

我喜欢这篇文章并不是因为它的特定模式，而是因为它深入研究了使用 spread 操作符时发生的事情。此外，它谈到了操作的复杂性，并使用了大 O 符号，这在前端世界是罕见的。:P

##### [JavaScript 是 web 的 CO2【音频】](https://changelog.com/jsparty/80)

(Bias Alert -我主持了这个播客)这是与 Chris Ferdinandi“香草 JavaScript 家伙”的一次有趣的讨论，讨论的范围从“香草”实际上意味着什么，为什么您可能想要避开更高级的框架，人们如何学习和面临什么挑战，以及一组伟大的香草 JavaScript 技巧、诀窍和资源。

##### [函数式 JavaScript:递归归约遍历树](https://jrsinclair.com/articles/2019/functional-js-traversing-trees-with-recursive-reduce/)

有很多计算机科学的数据结构在前端开发中并不常见，但是树是我们非常熟悉的。DOM 是一棵树，虚拟 DOM 是树，我们所有基于组件的架构都是树…所以使用 reduce 研究一些树相关的算法是非常有价值的。

##### [对未来做出反应，悬念迭起](https://dev.to/bnevilleoneill/the-future-of-react-unfolding-with-suspense-7pp-temp-slug-5919953)

React 中即将推出的一项功能非常吸引人。我喜欢 React 团队不断创新并推动生态系统向前发展的方式。在这种情况下，它允许以非常简单的声明方式处理可变的网络时间和异步性。

##### [你需要知道的关于 Ivy 的一切，全新的 Angular engine！](https://blog.angularindepth.com/all-you-need-to-know-about-ivy-the-new-angular-engine-9cde471f42cf)

我不做 Angular，但阅读这篇文章并看到创新在框架之间的生态系统中传播的另一个例子是很有趣的。这篇文章特别强调了 Angular 的最新版本如何支持动态导入和代码分割，以及像高阶组件这样的功能。或许对派对来说有点晚，但也快了。

#### 其他牛气

##### [微前端:好的、坏的、丑的](https://dev.to/kball/microfrontends-the-good-the-bad-and-the-ugly-2b3c)

(偏见警告:这是我写的)上周有很多关于微前端的话题和自发的模因，所以我写了一篇后续文章来分析这种架构方法的利弊。

##### [如何切分你的 HTML](https://css-tricks.com/how-to-section-your-html/)

利用 HTML5 的切片元素的优秀、全面的参考件。如果您对何时使用有任何疑问，

<aside>，，或者这将是一个很好的书签资源。</aside>

##### [如何从您的 Web 应用程序创建 PDF](https://www.smashingmagazine.com/2019/06/create-pdf-web-application/)

这在大多数情况下是无关紧要的，但是当请求创建一个可下载的 PDF 时，知道你的选择真的很好。

##### [优化谷歌字体性能](https://www.smashingmagazine.com/2019/06/optimizing-google-fonts-performance/)

我是谷歌字体的超级粉丝。通过提供一套免费的、易于使用的字体，他们极大地提高了在网络上用字体做有趣事情的能力。但是男孩，如果你不小心，你会毁了你的表演。这是一个很好的资源来了解他们为你做了什么……以及在使用谷歌字体时，为了保持一个高性能的网站，你还需要小心做些什么。

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
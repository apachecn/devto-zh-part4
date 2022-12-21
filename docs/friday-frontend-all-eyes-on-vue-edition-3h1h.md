# 周五前端:所有人都在关注 Vue 版

> 原文：<https://dev.to/kball/friday-frontend-all-eyes-on-vue-edition-3h1h>

希望你度过了愉快的一周！上周末是 Vue.js 世界疯狂的一周，因为最近的 RFC(实际上在我们 6 月 14 日的新闻简报中有链接)引发了大量的争议和反对。下面是几篇更深入探索 RFC 的文章，这足以引发一个主题，所以我们也有一些额外的 Vue 文章。

和往常一样，也有一些很棒的 CSS 和其他关注网络的文章。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [用 CSS 网格构建会议日程](https://css-tricks.com/building-a-conference-schedule-with-css-grid/)

这太棒了。我喜欢他强调 grid 如何使用常规的增量单位(在本例中是半小时的增量)的方式，但随后添加了命名的网格线，使实际的样式非常易读。不知道你能不能拿到比`grid-row: time-0900 / time-1030; grid-column: track-1;`更多的语义 CSS。

##### [CSS 缩放:外观 vs 布局属性](https://levelup.gitconnected.com/css-at-scale-cosmetic-vs-layout-properties-a1c8acc875d7?sk=4ab03d819d9ddc94acb7dfcf37f8dc76)

这是一个超级重要的概念；我也听说过这种区分被描述为“图形”属性和“结构”属性，但是不管你怎么称呼它，理解影响页面布局的属性和那些仅仅应用“耀斑”或者改变你正在操作的项目的视觉属性的属性之间的区别是一个关键的概念。

##### [CJSS:一个基于 CSS 的 web 框架](https://github.com/cjss-group/CJSS)

这太搞笑了。你听说过 CSS-in-JS 吗？这就是 JS-in-CSS。更不用说 HTML-in-CSS 了。它是一个构建时框架，使用您的 CSS 文件作为事实的来源，并从中生成 HTML 和 JavaScript 文件。不是为了严肃的用途，但是非常有趣和发人深省。

##### [纯 CSS 唯一人像- Isla](https://codepen.io/ivorjetski/pen/dBYWWZ)

在“神圣的烟雾，这是疯狂的”灵感类别中，看看这幅完全用 HTML 和 CSS 制作的肖像。作者评论说，他花了“大约 100 个小时”，并包括一个加速视频显示一些过程。一定要点击进入那个视频…事实上，它太棒了，我会在这里直接加上一个链接。

#### JavaScript

##### [Vue 最黑暗的一天](https://dev.to/danielelkington/vue-s-darkest-day-3fgh#0)

这是针对最近对 Vue.js functional API RFC 的一系列极其负面的反馈而写的，既是对负面意见的反驳，也是一组基于代码的示例，展示了新 API 如何使代码更加干净。

##### [来自 Vue 新功能 API RFC 的 3 个关键见解](https://dev.to/kball/3-key-insights-from-vue-s-new-functional-api-rfc-5467)

(偏见警告-我写了这个)我自己对 Vue.js 函数式 API RFC 的看法，重点是它可以告诉我们关于前端开发的未来。这里有大量有价值和积极的东西在推动网络向前发展。

##### [无视图 Vue 无渲染组件简介](https://dev.to/ycmjason/vue-without-view-an-introduction-to-renderless-components-23ld)

使用组件作为划分与呈现模板没有直接关系的逻辑和功能的一种方式，看起来很有趣。虽然我个人不太喜欢这个例子，但我认为这个方法非常有价值。对我来说，使用这种方法更有意义的一种情况是创建与 maps APIs 等 API 交互的组件，如 VueConf 的 [this talk](https://www.vuemastery.com/conferences/vueconf-us-2018/a-react-point-of-vue-divya-sasidharan/) 所示。美国 2018。

##### [在写下一个 Vue.js 组件之前你应该知道的 10 件事](https://dev.to/treyhuffine/10-things-you-should-know-before-writing-your-next-vue-js-component-1ign-temp-slug-9674555)

如果你相对来说是 Vue.js 的新手，你可能不会遇到有用的技巧的可靠列表。它们中没有一个是惊天动地的，但它们都是有用的。

##### [忘记组件的生命周期，开始考虑效果](https://sebastiandedeyne.com/forget-about-component-lifecycles-and-start-thinking-in-effects/)

很好地描述了 Hooks API 如何将我们从实现副作用的命令式风格(“挂载时做 x，卸载时做 y”)引导到实现副作用的声明式风格。

#### 其他牛气

##### [在漫画中渲染尖刻的网络评论](https://www.zachleat.com/web/snarky/)

这绝对是令人愉快的，虽然这是几个星期前的事了，但我认为上周所有的负面情绪都在蔓延，这非常值得看看这样的方式来让事情变得轻松起来。

##### [将服务人员引入谷歌搜索](https://web.dev/google-search-sw/)

一个引人入胜的案例研究，着眼于在 Google 搜索上实现服务人员时遇到和克服的所有挑战。谷歌的搜索功能可能是这个星球上对性能最敏感的页面，[每秒加载超过 63，000 次](https://seotribunal.com/blog/google-stats-and-facts/)，所以他们的挑战非常独特，但他们吸取的一些经验教训具有广泛的适用性。

##### [加速慢速网站的最简单方法](https://www.machmetrics.com/speed-blog/the-1-easiest-way-to-speed-up-a-slow-website/)

这肯定是一些低挂水果，但我有网站，我没有做好。有问题的方法？图像优化！这篇文章提供了一些关于这个问题有多普遍的数据，问题的范围，以及一些关于你能做些什么来解决它的建议。

##### [落帽&设计系统](https://product.voxmedia.com/2019/6/17/18524029/the-ballad-of-drop-caps-and-design-systems)

在 web 上以一致的方式实现首字下沉非常困难。我记得 ZURB 基金会的一名公关人员试图处理这件事，收到了 100 多条评论、多个版本和迭代……但仍然没有被合并。本文介绍了 Vox Media 最终面临的一些挑战以及不同的方法和解决方案。

##### [无障碍网页排版的 5 个关键](https://betterwebtype.com/articles/2019/06/16/5-keys-to-accessible-web-typography/)

这是一篇优秀的文章，讲述了使排版在网络上变得容易理解的要素。作为一个使用标准网络浏览器浏览但视力非常糟糕的人，我非常欣赏这些技巧，它们允许根据用户偏好调整字体大小，并保持实际观察到的高对比度。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
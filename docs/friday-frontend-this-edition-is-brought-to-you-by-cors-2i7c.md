# 星期五前端:这一版是由 CORS 带给你的

> 原文：<https://dev.to/kball/friday-frontend-this-edition-is-brought-to-you-by-cors-2i7c>

希望你度过了愉快的一周！本周科技/网络世界的大新闻是流行的 Zoom 视频播放器的零日漏洞，下面的一个链接中有一些有趣的信息，关于这个漏洞如何可能是由于对 CORS 的误解。出于这个原因，我还包括了对 CORS 的精彩介绍，以及您通常所怀疑的优秀 CSS 和 JavaScript 资源。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

附注:我开始了新的项目:一对一的教练项目。这当然不适合所有人，但如果你一直在与不知所措作斗争，或者希望你的公司有一个导师，也许这适合你。来看看吧！

#### CSS&SCS

##### [CSS 列表、标记和计数器](https://www.smashingmagazine.com/2019/07/css-lists-markers-counters/)

这太棒了。这是我见过的对样式列表选项最彻底的分解，也是对新的`::marker`伪元素和 CSS 计数器的介绍。警告:你需要最新的火狐浏览器或最近的 Safari 浏览器才能看到`::marker`的运行，而且似乎只有火狐浏览器支持嵌套计数器。

##### [用剪辑路径制作动画](https://css-tricks.com/animating-with-clip-path/)

看看如何使用`clip-path`属性的动画来创建一系列的过渡动画，从一个内容无缝而漂亮地移动到另一个内容。

##### [学习 CSS 布局的好玩地方——第二部分:网格布局](https://stephaniewalter.design/blog/fun-places-to-learn-css-layout-part-2-grid-layout/)

关于学习 CSS 网格的各种可用资源的综合资源。我是[网格花园](http://cssgridgarden.com/)(榜单第一)的超级粉丝，其余的看起来也很棒。

##### [将新的 CSS 技术引入生产](https://sidigital.co/blog/bringing-new-css-techniques-to-production)

对大量较新的 CSS 特性(主要是网格和自定义属性)的全面概述，以及开始使用它们的想法，即使您可能不得不支持不理解它们的遗留浏览器。解释了如何创建回退，以及过度依赖它们的一些危险。

##### [稍微提醒一下，伪元素是孩子，着实有点。](https://css-tricks.com/a-little-reminder-that-pseudo-elements-are-children-kinda/)

有趣地观察了像`::before`这样的伪元素在 DOM 中被当作子元素对待的方式。

#### JavaScript

##### [React 中未来会让你的 App 崩溃的 8 种做法](https://dev.to/jsmanifest/8-practices-in-react-that-will-crash-your-app-in-the-future-2le5)

常见问题的完整列表，有些是 React 特有的，但许多是通用的。我很欣赏作者不仅列出了问题，而且对每一个问题都有很好的改进建议。

##### [不，TypeScript 中的 Getters 和 setter&JavaScript 不是没用的](https://www.freecodecamp.org/news/typescript-javascript-getters-and-setters-are-they-useless/)

有趣的是，看看 getters 和 setters 使用背后的思想，以及一些正确使用的例子。我希望作者能更深入，但那里的东西是坚实的。

##### [当心！棱角分明会偷走你的时间。](https://dev.to/oleksandr/beware-angular-can-steal-your-time-1jk2-temp-slug-2934042)

在 Angular 中常见问题的大总结。非常具体的角度，所以如果你不使用它就跳过这一点，但如果你有一些很好的建议在这里。

##### [使用 Vue.js 中的插槽](https://www.smashingmagazine.com/2019/07/using-slots-vue-js/)

虽然 Vue.js 和 React 有许多相似的方法，但与 React 中的 props.children 相比，Vue 中最大的习惯用法差异之一是插槽的使用。这是一个很好的概述，介绍了什么是插槽，它们是如何工作的，以及如何使用它们来解决许多不同的挑战。

##### [我们在使用 JavaScript 时犯的错误承诺](https://www.betamark.com/blog/mistakes-using-javascript-promises/)

我一直听说承诺和异步具有挑战性，所以我总是很高兴能找到一篇像这样有助于理清事情的好文章。这经历了一些常见的错误和挑战，使用承诺和如何修复它们。

#### 提升链接

##### [Udemy 上出售的所有编程课程。起价 10.99 美元](https://click.linksynergy.com/fs-bin/click?id=hIdOL5Z4eK4&offerid=507388.10631&type=3&subid=0)

虽然过去了一段时间，但是所有的软件开发课程现在都在 Udemy 上重新发售了！呜-呼！如果你想了解一个新的领域，现在是购买课程的好时机，即使你暂时没有时间，因为你可以非常便宜地买到课程。我最近一直推荐的课程包括[完整的 JavaScript 课程 2019](http://bit.ly/zd-complete-javascript) 、 [Modern React with Redux](http://bit.ly/zd-modern-react) (我学习 React 的课程，更新了所有最新功能)，以及 [Vue JS 2 -完整指南](http://bit.ly/zd-vue2-complete)(由我最喜欢的 Udemy 导师之一马克西米利安·施瓦兹米勒(Maximilian Schwarzüller)主讲。)

#### 其他牛气

##### [内容还是设计先行？](https://www.vanschneider.com/content-or-design-first)

对内容和设计之间的关系进行深思熟虑的审视，以及设计师可以采取的一些步骤，以便更好地与文案合作。老实说，我认为这些想法对开发者来说都很重要。你还可以说“不要对用户生成的内容使用占位符副本”来进一步扩展它。强迫你自己(或你的项目经理)获得你计划使用的 UGC 类型的现实的“真实生活”的例子，并尽快将其放入你的开发者数据中。你会发现无数的边缘案件。

##### [带有“动态点击区域”的菜单](https://css-tricks.com/menus-with-dynamic-hit-areas/)

创建带有动态弹出菜单的“大菜单”的超酷解决方案，使用 SVG 创建动态“点击区域”以避免“移动鼠标时意外隐藏”的尴尬，这种尴尬很容易因使用天真的方法`:hover`而导致。

##### [响应式设计基本规则](https://polypane.rocks/blog/responsive-design-ground-rules/)

在多设备的世界里，思考、设计和开发的一套伟大的指导方针已经成为我们的标准。如果你在设计界呆过，你可能听说过其中的大部分，但我怀疑你会发现一些新的珍宝…我想我最喜欢的对我来说是新的是“从内容到设计”...aka 而不是基于设备大小的断点”扩展到看起来像狗屎。断点时间到了！”。爱死了！

##### [开发商不了解 CORS](https://fosterelli.co/developers-dont-understand-cors)

让我们看看最近的 [Zoom zero day 漏洞](https://medium.com/bugbountywriteup/zoom-zero-day-4-million-webcams-maybe-an-rce-just-get-them-to-visit-your-website-ac75c83f4ef5)背后对 CORS 的根本误解。非常有趣，绝对值得一读。

##### [CORS——现代网络中的跨地域传播](https://alazierplace.com/2019/06/cors-cross-origin-communication-in-the-modern-web/)

如果你是其中之一(很多！)开发者被 CORS 搞糊涂了，或者上面的文章没有让你点头称是，这个关于 CORS 如何工作的解释可能会有所帮助。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
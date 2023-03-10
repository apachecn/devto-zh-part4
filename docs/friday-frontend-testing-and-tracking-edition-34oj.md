# 星期五前端:测试和跟踪版

> 原文：<https://dev.to/kball/friday-frontend-testing-and-tracking-edition-34oj>

希望你的一周过得愉快，如果你在美国，你会度过一个愉快的劳动节假期。

这个星期有两个主题引起了我的注意:我再次寻找在前端进行测试的正确方法。我包括两篇文章，一篇是关于基于状态机的测试，这让我大吃一惊(以一种好的方式！)和另一篇更传统的单元测试文章。值得并排看两个。

第二个主题是浏览器供应商(Firefox 和 Brave)和广告公司之间关于跟踪主题的不断增长的推/拉。谷歌是房间里的大象，两边都有团队。对我来说，这是目前正在进行的大辩论之一，它将塑造互联网的未来。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [CSS 中溢出和数据丢失](https://www.smashingmagazine.com/2019/09/overflow-data-loss-css/)

CSS 布局技术的发展如何改变了我们对数据溢出和布局的看法，以及它如何改变了我们对网页设计的看法。

##### [如何使用 CSS 混合模式和 SVG 动态改变产品图像的颜色](https://tympanus.net/codrops/2019/09/03/how-to-dynamically-change-the-colors-of-product-images-using-css-blend-mode-and-svg/)

这是一个有趣的例子，展示了如何用精心制作的 SVG 和一些 CSS 来改变照片的颜色。混合模式的效果非常迷人，因为它将颜色合并到背景中，所以即使图像上有阴影和其他“缺陷”，颜色也会正确更新。

##### [理解 CSS 中的特异性](https://alligator.io/css/understanding-specificity-in-css/)

特殊性仍然是令初学 CSS 的开发人员最困惑的话题之一。有时，即使是已经编写了一段时间 CSS 的开发人员也很难理解它是如何工作的。这是一个很好的逐步指南，说明什么是特异性以及它是如何工作的。

##### [用 CSS 网格解决问题:甘特图](https://webdesign.tutsplus.com/tutorials/solving-problems-with-css-grid-the-gantt-chart--cms-33837)

CSS Grid 不仅简化了我们的 CSS，还使它更具可读性，这是一个很好的例子。我喜欢使用命名网格线的想法，以便能够为布局项目创建一个基本的语义“微型 dsl”。在一个代表一周中每一天的网格中，用`grid-column: mon-morning / sat-night;`来显示持续时间让人感到难以置信的满足。

#### JavaScript

##### [用状态机进行基于模型的测试](https://css-tricks.com/model-based-testing-in-react-with-state-machines/)

非常有趣的是使用状态机来自动生成测试。我以前见过类似的方法，但几乎都是在测试模型逻辑的后端。看到它被用来测试用户界面是非常有趣的。

##### [知道要测试什么——Vue 组件单元测试](https://vuejsdevelopers.com/2019/08/26/vue-what-to-unit-test-components/?utm_campaign=kwt&utm_medium=article&utm_source=vuejsnews)

关于测试的另一个观点，本文着眼于单元测试的边界应该是什么样子，特别是对于 Vue 组件。这看起来很像我对测试的看法，尽管坦白地说，在读完上面的状态机测试文章后，我感觉像是倒退了一步。结合以上值得一读，让我知道你的想法！

##### [JavaScript 知道反应过来](https://kentcdodds.com/blog/javascript-to-know-for-react)

对 React 文档和特性中经常“假定”的大量 JavaScript 特性的深入研究，可能会让 React 新开发人员感到困惑，特别是那些已经使用 JavaScript 一段时间但可能没有跟上最新语言特性的开发人员。

##### [JavaScript 符号实用指南](http://thecodebarbarian.com/a-practical-guide-to-symbols-in-javascript.html)

符号是我在其他语言(尤其是 Ruby)中使用过的一个特性，但以前从未在 JavaScript 中使用过。也就是说，读完这篇文章后，我想我要开始了——特别是区分用户数据和程序数据以及防止原型污染和函数覆盖等问题的能力看起来超级酷和强大！

##### [Javascript 递归快速入门](https://www.freecodecamp.org/news/quick-intro-to-recursion/)

我差点没把这个包括进去，因为感觉超基础。在任何计算机科学课程中，递归都是一个非常早期的话题。但是我在阅读时意识到——我很少在 web 开发教程或其他 JavaScript 相关的学习中看到递归。对于那些没有计算机科学背景的人来说，它可能不在你的工具箱里。而如果不是，就应该是；有些问题要递归解决要简单得多。所以如果你的工具箱里没有，读一下这个——这是一个超级简单快捷，但是清晰的递归概念介绍。

#### 其他牛气

##### [今天的火狐默认阻止第三方跟踪 Cookies 和密码挖掘](https://blog.mozilla.org/blog/2019/09/03/todays-firefox-blocks-third-party-tracking-cookies-and-cryptomining-by-default/)

隐私和个人数据的问题是目前科技社区中的一个大问题，Firefox 在用户隐私方面采取了非常明确的立场。在他们的最新版本中，他们默认阻止第三方 cookies。

##### [勇者揭秘谷歌 GDPR 变通办法](https://brave.com/google-gdpr-workaround/)

这可能更有趣，一家基于 Chromium(主要由谷歌支持)开发浏览器的浏览器供应商(Brave)正就与 RTB 有关的隐私泄露问题将谷歌告上法庭。如果你在广告技术行业工作，或者你的生意依赖于广告费，这是非常值得关注的。

##### [一个网站应该不用 JavaScript 就能工作吗？](https://css-tricks.com/should-a-website-work-without-javascript/)

围绕发生在 [JSParty #87](https://changelog.com/jsparty/87) 的辩论，有一系列非常有趣的想法和问题。(偏见警报——我在原集中)。最有趣的方面不是我们是否应该使用 JS，而是我们如何根据手头的问题调整我们的工具。也就是说，我们如何确保在正确的问题上使用 JS，而不是在简单的解决方案可以解决问题时过度应用复杂的框架或库？

##### [取消 vs 关闭:设计区分差异](https://www.nngroup.com/articles/cancel-vs-close/)

取消和简单地关闭一个部分之间的区别是微妙的，但是我知道作为一个用户，我已经不止一次地被这样的 ui 所迷惑，当我期望另一个的时候，它却做了一个。这篇文章很好地讨论了两者的区别，以及如何做出一个清晰的、不会让用户感到困惑的设计。如果你是一名设计师，这是一本必读的书，但即使你是一名前端开发人员，我也建议你阅读这本书，这样你就知道应该对哪些设计提出质疑。

##### [工具提示的问题以及如何处理](https://adamsilver.io/articles/the-problem-with-tooltips-and-what-to-do-instead/)

另一篇关注设计的优秀文章，着眼于另一种常见的 web 模式:工具提示！工具提示超级超级常见，但是正如这篇文章指出的，当涉及到移动设备上的可访问性和交互时，会有各种各样的问题。我喜欢作者不仅指出问题，而且包括替代方法以及为什么/何时它们更好的方式。

* * *

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
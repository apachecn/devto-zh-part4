# 周五前端:新媒体查询版

> 原文：<https://dev.to/kball/friday-frontend-new-media-queries-edition-2ooe>

希望你这周过得愉快！本周在 CSS 部分，我们有几篇关于新媒体查询以及如何使用它们的文章。

我特别兴奋地看到关于在更多地方使用`prefers-reduced-motion`查询的信息，以及一些非常酷的机会来为那些与运动斗争的人自动改善网络。

也有一些很棒的 JavaScript 文章，其中大部分集中在“相对普通的”JavaScript 上，还有一篇文章认为，嘿，也许我们还不应该扔掉 jQuery。:)最后发布了几个大消息——一个跟踪被攻击数据的新服务，以及一个联合包管理器。尽情享受吧！

最佳，

来自 ZenDev 的 KBall

#### CSS&SCS

##### [用像素减少运动](http://bradfrost.com/blog/post/reducing-motion-with-the-picture-element/)

不完全是 CSS，但是你知道吗，带有 T1 的 T0 可以基于它从 T2 媒体查询中下载的图像。太酷了！我可以想象在帖子中用手动和自动的方式来提供静态的动态 gif，以保护那些患有光敏性癫痫发作的人。

##### [创建用户控制的暗或亮模式](https://andy-bell.design/wrote/create-a-user-controlled-dark-or-light-mode/)

一步一步的教程，创建一个用户可切换的黑暗或光明模式，关闭`prefers-color-schema`媒体查询。这不仅是对该查询的一个很好的介绍，也是如何使用 css 自定义属性构建主题的一个很好的例子。

##### [将 CSS 背景图片属性作为反模式](https://nystudio107.com/blog/the-css-background-image-property-as-an-anti-pattern)

概述了在我们使用的大多数地方使用`background-image`属性的问题，以及如何使用`picture`元素来处理大多数首先需要使用背景图像的情况(比如封面尺寸)。

##### [CSS 架构——文件夹&文件结构](https://medium.com/@elad/css-architecture-folders-files-structure-f92b40c78d0b)

文件结构是在建立 CSS 架构时做出的“高性价比”选择之一。这篇文章是一个很好的例子，展示了一个长期的 CSS & HTML 专家是如何使用他的方法的。

#### JavaScript

##### [初级 JavaScript 开发人员需要学习的五大技能](https://dev.to/kball/top-5-skills-to-learn-as-a-junior-javascript-developer-1o4a)

(偏见警报——我写的这个)。“我应该关注什么”是 JavaScript 世界中反复出现的主题，因为变化太快了，很难知道应该关注什么。我们最近在 JSParty #77 上就这个话题进行了一次很好的对话，在这篇博客文章中，我列出了我们一致认为作为初级 JavaScript 开发人员应该重点关注的五大技能。

##### [JavaScript 中把值转换成字符串的 5 种方法](https://medium.com/dailyjs/5-ways-to-convert-a-value-to-string-in-javascript-6b334b2fc778)

讲述将值转换为字符串的各种方法，以及它们如何处理各种边缘情况。

##### [在 JavaScript 中实现私有变量](https://css-tricks.com/implementing-private-variables-in-javascript/)

总结了在一种实际上不支持私有变量的语言中拥有“私有”变量的所有不同方法。以对私有类字段的新的实际私有提议的预告结束。其中，我使用模块设计模式最多，但它们都有其用途。

##### [为什么 2019 年我还在用 jQuery](https://arp242.net/jquery.html)

对于将现代 JavaScript 框架用于一切事物的趋势，这是一个有趣的对比。也采用了一点'[你可能不需要 jQuery](http://youmightnotneedjquery.com/) 参数。

##### [Vue JS 中计算属性介绍](https://blog.bitsrc.io/introduction-to-computed-properties-in-vue-js-32061faa882f)

计算属性是我最喜欢的 Vue 特性之一，因为它们允许你用一种声明性的方法来表达你的数据，而不仅仅是你的模板。你可以说“嘿，这是如何定义这些数据的”,框架负责保持数据的更新。我喜欢它。这篇文章很好地介绍了这个概念以及它们是如何工作的。

#### 提升链接

##### [Udemy 夏季大减价:大部分课程 9.99 美元！](http://click.linksynergy.com/fs-bin/click?id=hIdOL5Z4eK4&offerid=507388.10404&type=3&subid=0)

几乎每道菜的价格都很低。大量优秀的内容可供选择，现在就去购买，然后在你最需要的时候完成课程。我最近一直推荐的课程包括[完整的 JavaScript 课程 2019](http://bit.ly/zd-complete-javascript) 、 [Modern React with Redux](http://bit.ly/zd-modern-react) (我学习 React 的课程，更新了所有最新功能)，以及 [Vue JS 2 -完整指南](http://bit.ly/zd-vue2-complete)(由我最喜欢的 Udemy 导师之一马克西米利安·施瓦兹米勒(Maximilian Schwarzüller)主讲。(顺便说一句，如果你正在寻找其他 webdev 内容，浏览一下[他的教师页面](http://bit.ly/zd-maxschwartz)并查看他的其他课程可能是个不错的主意)

#### 其他牛气

##### [值得了解的不寻常 HTML 标签🧩](https://dev.to/christopherkade/unusual-html-tags-worth-knowing-3gbk)

语义 HTML 标签让你创建更易访问的应用程序和网页。你越是利用这些，你就越能让浏览器和工具供应商让你的内容具有可访问性，而不是你自己去做。我不知道这些标签的数量(有一个缩写标签！？！？)但即使意识到了它们，我也会开始更多地使用它们。

##### [Entropic:任何东西的联邦包注册中心](https://github.com/entropic-dev/entropic)

在 JSConf EU 的一个广受欢迎的演讲中宣布的这个新的联邦包管理器已经成为 JS twitter 过去几天的话题。在这和最近的 github 声明之间，看起来我们正在为软件包管理人员寻找一个多语言的生态系统，但对于创建一个不受盈利公司控制的联合方法还有很多要说的。

##### [火狐监视器](https://monitor.firefox.com/)

这太棒了！一个超级友好的前端和订阅方式，本质上是“我被 powned 了吗”-即，给定我的电子邮件地址，哪些服务被黑客攻击，释放了我的信息，以及到底是什么被泄露了。

##### [你和灯塔的第一次演出预算](https://bitsofco.de/your-first-performance-budget-with-lighthouse/)

哇，我听说过性能预算，但我不知道 Lighthouse 有工具来明确标记您的预算，并在您超出预算时进行捕捉。非常酷！

#### 周五快乐！

这就是本周的周五前沿时事通讯。如果你喜欢这个，你也许应该在 Twitter 上关注我或者加入我的邮件列表。注册后，每周五你就可以直接收到这些时事通讯了！在这里报名:【https://zendev.com/friday-frontend.html T2】
# 渐进增强

> 原文：<https://dev.to/remy/progressive-enhancement-4j2m>

这篇文章最初写于 2015 年，但今天重读时，它仍然(差不多)站得住脚，所以我最终点击了发布。T3】

我原以为一个 [EdgeConf](https://edgeconf.com/2015-london) 面板是关于不使用 JavaScript 的开发者的，因为他们对构建高端 web 应用更感兴趣，充满了 WebRTC、Web Audio 之类的东西。但事实并非如此。

我有幸在 2015 年介绍了渐进式改进专家组并为该专家组做出了贡献。对于我的介绍，我做了一些“研究”,并思考了什么是渐进式改进。

事情是这样的:在得到 800 多名开发者的回复(在 Twitter 上投票)后，我开始意识到大多数开发者，或者肯定是每个关注我的人，每个观看(EdgeConf 流)的人，每个阅读的人，都把渐进式改进看作是一件好事。做“正确的事情”。他们明白它可以将网站的内容传递给更广泛的受众。毫无疑问。

有可访问性和搜索引擎优化的好处。我直接从开发人员那里听说，SEO 是企业已经接受的一种采用 PE 方法进行开发的方式。

但事实是:渐进增强不是工作流的一部分。

## 什么是渐进式增强？

良好的...这是 Steve Champeon 创造的一个术语，他用它来描述他(或者他和他的团队)用来建立网站的技术，而不是一种优雅的退化方法。

因此，没有一条单独的线来定义渐进增强。然而，维基百科将其定义为:

> [渐进式增强]允许每个人使用任何浏览器或互联网连接访问网页的基本内容和功能

适度降级与相反，完整的功能被交付给浏览器，边缘情况和“旧浏览器”(不满足技术要求)降级到(潜在的)较少的功能。

**问题是**根据对我自己的追随者的调查，也就是说，当谈到 web 开发时，他们可能有相似的兴趣和价值观，800 名开发人员中有 25%仍然认为渐进式改进只是让网站在没有启用 JavaScript 的情况下工作。

## 没有 JavaScript 怎么让它工作？

我可以想象，对于任何刚开始从事 web 开发的人来说，这个问题可能会令人望而生畏。首先，他们被要求解决一些复杂的问题，最后，他们通过结合使用 StackOverflow 复制和粘贴以及最近从书籍和其他资料中获得的建议，终于解决了如何让它工作的问题……但是，现在突然之间:不用代码就能让它工作😱

这解释了我一次又一次听到的*银弹回应*:“WebRTC 聊天网站如何工作？”…不会的。

事实上，这是非常聪明的杰克·阿奇博尔德的优秀的 [SVGOMG](https://jakearchibald.github.io/svgomg/) 网站…关闭了 JavaScript，看着沮丧沸腾，我只能把我的电脑扔出窗外…

抛开无聊的笑话不谈，一个网站没有 JavaScript 如何工作真的不是一个好问题。其实完全是断章取义。

一个更好的问题可能是**我们如何交付一个可以满足最少需求的基线网站**。

杰里米·基思最近回应批评说，用今天的期望逐步提高一切是不可能的。渐进式增强是:

> ...弄清楚什么是核心功能，什么是增强功能。

那么，网络社区如何重新构建它的思想，并把渐进增强作为你的基础呢？

## 为什么有关系？

今天，许多开发人员正在编写“胖客户端”，也就是说，JavaScript 驱动了浏览器中的许多功能和表示。

他们通过在浏览器中交付和呈现视图来实现。这样做的好处是网站对用户输入的速度非常快。另一个很大的好处是，有很多框架(React、Vue、Angular、Polymer 等等)非常适合客户端 MVC，即客户端代码中的完整应用程序逻辑。

问题是这些框架经常会(试图)重新发明网络体验的基本构件。几个简单/经典的例子:

*   该链接根本不是链接，这意味着您不能在新标签页中打开它，也不能复制或共享它...或者甚至按你期望的方式点击它
*   *按钮*不是按钮
*   您不能共享您正在查看的页面的链接(因为它都是客户端渲染的，没有链接)
*   屏幕阅读器无法正确浏览内容

我最近写了一篇关于我如何辜负了主播 T1 的文章。这基本上触及到了*所有*上面的要点。

这并不意味着这是不可能的，只是它经常被遗忘。同样，Flash 也经常被贴上“不可接近”的标签。这不是真的，让 Flash 可访问是有可能的，只是默认的开发路径不包括它。

一个更极端的例子是 Flipboard 的移动网站。重要的是:*移动站点*。Flipboard 使用一个`canvas`元素呈现整个页面。我不能保证网站的可访问性，但是在手机上它表现得很好。感觉...“原生”。与此同时，它也被打破了。我不能复制链接，也不能复制文本——类似于 Flash 应用程序甚至 Java 小程序时代。它看起来很棒，但没有“web”的感觉<sup>。</sup>

警告:这在 2015 年是真的，这是可能的…可能已经被扔掉并修复了…我希望如此。

问题是:与人们经常拿来比较的专有和封闭平台相比，浏览器相当糟糕。

有压力(来自 SF/Apple/谁知道)要提供感觉“本土”的网站(不，我不会定义这个)，浏览器总是在追赶本土的专有平台:这是事实。

原生媒体元素、原生套接字、原生音频、原生推送通知、原生网络控制——所有这些都花费了浏览器的快乐时光。因此，当一家公司决定在文章列表中尝试和测试的风格不会给他们想要的独特的 UX*和*60 fps 的交互时，他们当然会烘烤自己的技术(在 Flipboard 的情况下，重新发明画布轮子...*号和*号[号](http://ajaxian.com/archives/bespin-a-new-mozilla-labs-experimental-extensible-code-editor-using-canvas)号完全一样。

## 但是...没有 JavaScript，胖客户端如何工作？

例如，Angular 没有一个开发者故事来说明如何开发一个以渐进增强为基线的站点。

这是不是意味着不可能？我不这么认为。然而，如果没有故事，开发人员将会被已解决的问题所吸引(这是可以理解的)。

当框架是项目的先决条件时，这个故事看起来像什么？

## Web 组件

Web 组件是一个热门的争论话题。它们会导致网络的各种混乱。另一方面，它们也非常适合渐进增强。

以下面的 HTML 为例:

```
<input type="text" name="creditcard" required autocomplete="cc-number"> 
```

注意，我没有使用`pattern`属性，因为很难正确匹配信用卡(它们可能在数字组之间有空格，或者破折号，或者根本没有)。

也没有验证，第一个数字也告诉我们使用的是哪种卡(例如，4 表示 Visa 卡)。

例如，web 组件可以逐步增强元素，类似于浏览器本身增强`type="date"`的方式。

```
<stripe-cc-card>
  <input type="text" name="creditcard" required autocomplete="cc-number">
</stripe-cc-card> 
```

我想知道，web 组件是渐进增强的未来吗？

## 潜在的问题即将出现

开发者天生懒惰。这让他们/我们优化了我们的工作流程，变得非常擅长解决问题。我们重复使用已知的有用的东西，并倾向于弥补我们“没有也能生活”的复杂部分。可悲的是，这可能是以可访问性和渐进增强为代价的。

我认为有一些更大的潜在问题即将出现:ES6 - esnext(即 JavaScript 的未来)。

### “但是渐进增强和 ES 无关——随便……”

退一步想一想。假设我们正在编写一个纯 HTML 的网站(没有 CSS 或 JS)。但是我们要用最新最神奇的*原生*邮件验证:

```
<input type="email" required> 
```

简单。但是...如果不支持`type="email"`*会怎么样？嗯，没什么不好的。该元素将是一个文本元素(我们可以在服务器上验证)。*HTML 不破*。*

JavaScript 并不完全相同，但是我们可以防御性地编码，在适当的地方使用特征检测和聚合填充。

ES6 具有打破这种设计的特性。语法中断特性不能与我们的 ES5 共存，也不能多填充。它一定是透明的。

目前有关于智能管道的讨论，它可以向“旧”浏览器提供多填充代码，并向那些新浏览器提供轻量级的原生 ES-x 特性。不过，我认为旧的浏览器会在旧的机器上运行，因此在 JavaScript 包中有更多代码的情况下性能不会很好。相比之下，在新机器上运行的新浏览器可能更快，在运行大量代码方面可能比老一代浏览器更有能力。 <abbr title="I don't know">IDK</abbr> ，略一思索。

### 语法中断

有少量 ES6 特性是语法中断的，特别是"[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)。

这意味着，如果不支持 ES6 箭头的浏览器遇到箭头函数，就会导致语法错误。如果该网站遵循最佳实践，将所有的 JavaScript 合并到一个文件中，这意味着*所有的*JavaScript 都崩溃了(我个人在 JS Bin 上看到过这种情况，当时我们使用了 [jshint，它使用了 ES5 setters 并破坏了 IE8](https://github.com/jsbin/jsbin/blob/b1e56287d668818b361bc71b299fc36b707f9e6e/views/index.html#L311-L320) )。

我已经询问了 TC39 小组的成员和 JavaScript 专家，这里的正确方法是什么(记住这仍然是早期)。

答案是混合的:

*   使用特性检测(包括语法中断特性)并有条件地加载*右*脚本，ES5 或 ES6
*   将你的 ES6 转换到 ES5，并使两者都可用

这似乎很脆弱，而且越复杂，随着时间的推移，新的项目就越有可能遗漏 transpile 部分，忘记支持旧的浏览器——甚至是不支持 ES6 的新浏览器(可能是因为 VM 占用空间更小，并且必须在超低功耗的环境中工作)。

因为 JavaScript 不像 HTML 和 CSS 那样具有弹性，所以人们担心它会让无法升级的用户面对一个损坏的或空白的页面。

**有没有解决这个的工作流程？还是我们被迫在网络上支持两种不兼容的语言？**

* * *

感谢阅读。照例，**看情况**。事实上，这取决于，适用于我工作的每一个项目。

* * *

## 进一步阅读

*   [渐进增强仍然很重要](http://jakearchibald.com/2013/progressive-enhancement-still-important/)
*   [进取](https://adactio.com/journal/7706)
*   [每个人都有 JavaSript 吧？](http://kryogenix.org/code/browser/everyonehasjs.html)
*   [渐进式改进的真实成本](http://blog.easy-designs.net/archives/the-true-cost-of-progressive-enhancement/)
*   [瑞安航空的渐进式改进方法](http://christianheilmann.com/2015/05/24/the-ryanair-approach-to-progressive-enhancement/)
*   [当 JavaScript 似乎是一种需求时，如何应用渐进式增强](http://www.aaron-gustafson.com/notebook/how-to-apply-progressive-enhancement-when-javascript-seems-like-a-requirement/)
*   [我的渐进增强问题](https://plus.google.com/+ScottJenson/posts/S23BqQsEuvR) ( [@adactio 的回应](https://adactio.com/journal/7774))
*   [渐进强化:一堂历史课](http://cole007.net/blog/120/progressive-enhancement-a-history-lesson) ( [和他的阅读清单](http://readlists.com/c04e56c0/))
*   [为什么话语使用 Ember.js](http://eviltrout.com/2013/02/10/why-discourse-uses-emberjs.html)
*   有多少人错过了 JavaScript 增强？
*   [你忽略了服务器端渲染 JavaScript 应用的要点](http://tomdale.net/2015/02/youre-missing-the-point-of-server-side-rendered-javascript-apps/)
*   [渐进增强:泽德死了，宝贝](http://tomdale.net/2013/09/progressive-enhancement-is-dead)

*原载于[雷米夏普的 b:log](https://remysharp.com/2019/07/24/progressive-enhancement)T3】*
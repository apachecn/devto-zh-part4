# 上周前 5 名开发者评论

> 原文：<https://dev.to/devteam/top-5-dev-comments-from-the-past-week-2dl5>

这是你可能已经错过的令人敬畏的开发者评论的每周综述。欢迎并鼓励你使用 **[#bestofdev](https://dev.to/t/bestofdev)** 标签来发表帖子和评论。

这篇关于 **[为什么我不使用 web 组件](https://dev.to/richharris/why-i-don-t-use-web-components-2cia)** 的文章当然引发了很多热烈的讨论和回复。在这个帖子的顶部是来自[@ equinuscio](https://dev.to/equinusocio):
的这个(恭敬的)反驳

[![equinusocio profile image](img/305a52e698cc95f5d846788d5576d569.png) ](/equinusocio) [ Mattia Astorino ](/equinusocio) • [<time datetime="2019-06-20T13:42:28Z" class="date-short-year"> Jun 20 '19 </time> • Edited on <time datetime="2019-06-22T12:05:56Z" class="hidden m:inline-block date-no-year">Jun 22</time>](https://dev.to/equinusocio/comment/c5j0) 

很抱歉，但是不，你定义的大部分要点都是从对该技术的错误使用中提取出来的。然后:

**1 不是真的。**
即使禁用了 js，也可以渐进增强 web 组件。googlechromelabs.github.io/howto-c...

**2 不是真的。**
你不用把所有的样式都放在 shadow dom 里面，只放那些不应该组合或者访问的样式。就像私有 ts 函数一样。那么你有很大的灵活性。我建议检查这一点，并定义您的 web 组件的上下文。【css-tricks.com/making-web-componen...】T5
T6】

另一件事，主机元素是公开的，完全可由外部 css 定制，你应该在这方面努力。同样，只有私有的和功能性/结构性的 css 应该在影子树中。
[codepen.io/equinusocio/pen/vMOqBO](https://codepen.io/equinusocio/pen/vMOqBO)

当您编写真正的 CSS 并在 CSS 文件中生成它或者将它嵌入到`<style>`标签中时，CSS-in-JS 不是问题。第一个 css-in-js 工具的问题是，你必须编写伪 css json-like 并把它内联。现在，任何现代的 css-in-js 解决方案都允许您编写标准 css 并将其放在一个单独的文件中。

**3 和 4 直到 2018 年都是正确的**
现在所有的现代浏览器都完全支持 v1 规范。这意味着所有基于 webkit 和 chromium 的浏览器(包括 edge)。现代开发不需要 polyfill，如果你必须支持旧的浏览器，你可能已经在用 polyfill 做 js 了，那还有什么问题呢？你只是选择了一种新技术来开发旧的浏览器。

5 是正常的，而且完全有道理。

开槽元素不存在于放置`<slot>`标签的影子树中，它们存在于 light-dom 中，并且作为 light dom 中的任何 html 元素(例如在文档中),它们被立即呈现。Web 组件是自定义的 html 元素，如果你想添加一个惰性行为，你必须为你的组件实现它，就像你现在对任何 lazyload js 库做的那样。

对整个 HTML 来说都是如此
这些问题都与 HTML 有关，如果你使用简单的属性而不是抽象的，任何框架都会有这些问题。

* * *

Web 组件不是 React、Vue 或 Svelte 组件，它们不是逻辑容器，而是存在于文档中的真正的 html 元素，它们应该像任何其他 html 元素一样使用。把一个 web 组件和一个抽象的苗条组件相比较是完全误导的。他们做不同的事情。

我喜欢 sveltejs，我认为它是良好抽象之上的良好框架，你不需要写关于其他技术的虚假的东西。

## 我在这里不是要告诉你 web 组件是解决方案，或者它们比其他工具更好。但是我不喜欢比较两个不同事物的内容，并声称一个与另一个相比完全不好。

干杯！

回复新闻 **[美国部分监狱禁书编码](https://dev.to/ben/some-us-prisons-are-banning-books-on-coding-54jg)** [@joelnet](https://dev.to/joelnet) 谈监狱系统中非常黑暗的动机:

[![joelnet profile image](img/d77df0cc67c87968a2f6a59e88575a60.png) ](/joelnet) [ JavaScript Joel ](/joelnet) • [<time datetime="2019-06-22T03:52:08Z" class="date-short-year"> Jun 22 '19 </time>](https://dev.to/joelnet/comment/c7ec) 

这是故意的。营利性监狱系统旨在抓住你，留住你。

有重罪吗？你现在能做什么工作？狗屎工作。为了生存，你更可能需要偷窃或交易。你当然会回到监狱。

三振出局，你就是永久居民了。现在你获利了。

即使你想变得更好。形势对你非常不利。

我们需要监狱改革。营利性的需要去。

**[数学技能对于软件开发有多重要？](https://dev.to/ben/how-important-are-math-skills-for-software-development-347n)** [@rpalo](https://dev.to/rpalo) 提供一些见解:

[![rpalo profile image](img/d2df79a7640554be24029a559e2d464e.png) ](/rpalo) [ Ryan Palo ](/rpalo) • [<time datetime="2019-06-19T14:16:23Z" class="date-short-year"> Jun 19 '19 </time>](https://dev.to/rpalo/comment/c4h9) 

我会说，它们从来不会伤害到你，而且它们可以为你可能会错过的问题提供有价值的见解。但是它们有多必要可能取决于你在做什么。

对于网络工作，你可以少做一些，因为你更关注设计、UX、网络、性能、事件循环和数据争论，这些都以不同的方式很难，但与数学没有那么直接的联系。

对于更多的算法工作，使用图论、搜索算法、树等解决更复杂的问题。，更重要。它有助于基本理解您正在应用的方法，以及哪些算法可能在给定的场景中工作得最好。

对于数据科学来说，这不是绝对必要的，但如果您能够掌握数学和统计概念并以此为基础，而不仅仅是记住使用什么库和调用什么方法，这确实有助于加快您的学习。这也有助于你评估自己的结果，而不是因为“算法”这么说就盲目接受它们。这在伦理上变得越来越重要，因为越来越多的决策是通过人工智能、机器学习和大数据驱动的见解做出的。

对于低级系统编程，至少一点数论(事物的二进制、十六进制和十进制表示，以及如何在头脑中来回转换)是关键的。幸运的是，虽然数论令人生畏，但如果你慢慢地学习，并使用正确的例子，它最终并不超级复杂。我有几篇关于这个的文章。

最后，如果你所在的领域是数学密集型的，比如物理、工程、化学、生物、遗传、统计、概率等软件。，那么你可能会想对这个特定的领域有所了解。

我能想到的就这些了。我的哲学是，数学总是有助于你成为更好的开发人员，但要像对待我们必须学习的任何其他东西一样对待它。根据你的需要或兴趣来学习，如果你现在不知道所有的事情，不要觉得自己是个骗子。只要把它列在你的清单上，你就可以在闲暇时学习和观看 YouTube 上的一些数字爱好者视频。😁

这是一个相关的感觉和问题。如果你正在做一个项目，却发现它已经存在于这个世界上，你会怎么做？ [@nektro](https://dev.to/nektro) 分享了一些关于类似项目中不同观点的价值的智慧:

[![nektro profile image](img/17f9a26aac31fd56edadf8c06c9ef4db.png) ](/nektro) [ Meghan (she/her) ](/nektro) • [<time datetime="2019-06-18T18:04:02Z" class="date-short-year"> Jun 18 '19 </time>](https://dev.to/nektro/comment/c3h2) 

我做了很多东西，我开始做*是因为*已经存在了，我想拥有它。每个人都有独特的视角，用新的方法来完成旧的任务可以彻底改变它们。保持坚强💗

[@jaakidup](https://dev.to/jaakidup) 谈**中频繁更改框架的影响 [VueJS 已死，VueJS 万岁！](https://dev.to/stefandorresteijn/vuejs-is-dead-long-live-vuejs-1g7f)** :

[![jaakidup profile image](img/4947022a00b8f1c5acbfc2b10e8b888f.png) ](/jaakidup) [ Jaaki ](/jaakidup) • [<time datetime="2019-06-21T21:26:57Z" class="date-short-year"> Jun 21 '19 </time>](https://dev.to/jaakidup/comment/c788) 

给我的感觉就是所有的前端框架都在不断地试图去挠一个“可能/大概不存在”的痒处。

每个人都在努力让事情变得更好，这很好，但这些类型的变化不仅会影响你仍然要做的项目，还会影响你已经完成的项目。几个月之后，将会有另一个变化，进一步从一个项目中移除不同的 Vue(或者任何其他的 FE 框架)。

在这方面，我不喜欢整个开发社区成为框架开发的试验品。即。就在你弄清楚一支铅笔是如何工作的时候，有人去把它改了:)

我想我希望可以随时选择使用当前版本或下一个版本，直到无限。要求太多了吗？

下周见，更多精彩评论，✌
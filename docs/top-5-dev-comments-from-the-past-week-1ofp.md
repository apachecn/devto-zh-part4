# 上周前 5 名开发者评论

> 原文：<https://dev.to/devteam/top-5-dev-comments-from-the-past-week-1ofp>

这是你可能已经错过的令人敬畏的开发者评论的每周综述。欢迎并鼓励你使用 **[#bestofdev](https://dev.to/t/bestofdev)** 标签来发表帖子和评论。

回复 **[你能用十个字或更少的话给出的最好的职业建议是什么？](https://dev.to/ben/what-s-the-best-career-advice-you-can-give-in-ten-words-or-less-53op)** [@damcosset](https://dev.to/damcosset) 偶然发现一些你在开始职业生涯时应该牢记在心的事情:

[![damcosset profile image](img/248cab644d6d2ee0faede95fcea25c93.png) ](/damcosset) [ Damien Cosset ](/damcosset) • [<time datetime="2019-06-09T19:36:18Z" class="date-short-year"> Jun 9 '19 </time> • Edited on <time datetime="2019-06-09T19:36:31Z" class="hidden m:inline-block date-no-year">Jun 9</time>](https://dev.to/damcosset/comment/bjf1) 

别担心，没人知道他们在做什么。

在 **[中，许多高级工程师有哪些习惯是初级工程师应该尽量避免采用的？](https://dev.to/ben/what-habit-do-many-senior-engineers-have-that-juniors-should-try-to-avoid-adopting-2dd9)** ， [@sunnysingh](https://dev.to/sunnysingh) 关于聪明的诱惑的言论:

[![sunnysingh profile image](img/d859cf646f0f0ccf3acdd33288700665.png) ](/sunnysingh) [ Sunny Singh ](/sunnysingh) • [<time datetime="2019-06-08T20:32:45Z" class="date-short-year"> Jun 8 '19 </time>](https://dev.to/sunnysingh/comment/bikc) 

避免巧妙的代码。

你觉得写聪明的代码很聪明，你甚至可以说这是更少的代码。然而，代码应该是显式的，因此每个人都可以理解。即使这意味着要多写一点代码。

在开发之前，我从来没有意识到命名事物的重要性。在这方面，与 [@ben](https://dev.to/ben) 一起工作非常有益。我很喜欢看 [@gypsydave5](https://dev.to/gypsydave5) 对 **[的回复你给东西命名有流程吗？](https://dev.to/ben/do-you-have-a-process-for-naming-things-4i49)** :

[![gypsydave5 profile image](img/e8fb53c1729dbbe42da496ed974bb09f.png) ](/gypsydave5) [ David Wickes ](/gypsydave5) • [<time datetime="2019-06-06T21:45:48Z" class="date-short-year"> Jun 6 '19 </time> • Edited on <time datetime="2019-06-07T22:10:04Z" class="hidden m:inline-block date-no-year">Jun 7</time>](https://dev.to/gypsydave5/comment/bgnj) 

露骨？不。但这是我的经验法则。

#### 1。不要急于给事物命名。如果有的话，命名是抽象的开始。一旦某样东西有了名字，它就会给每个阅读它的人带来精神负担。在其他地方，糟糕的名字会导致糟糕的抽象。

##### 1.1。当你写软件时，你可能*不知道你在做什么*。你不明白你要构建的是什么，对于你要工作的领域，什么是正确的代码形式、组织和风格。坏名字通常是某人在开发过程的早期误解他们所构建的东西的证据。但是后来这个名字被保留了下来，因为每个人都在使用它...于是恶名泛滥。

##### 1.2。所以，如果有什么不同的话，我更喜欢一个完全不切题的可怕名字，而不是一个充满包袱和观点的名字。我觉得很好。

#### 2。如果你把`I`放在你的接口名称前面，我会以一种不友好的方式对你大喊大叫。这是 2019 年——你不需要用[匈牙利符号](https://en.wikipedia.org/wiki/Hungarian_notation)来表达变量的类型。如果有的话，接口应该命名一般情况，而实现应该命名具体情况。`Database`可以是接口，`PostgreDatabase`可以是具体类型。

##### [2.1](#21-if-your-interfaces-are-describing-behaviour-%C3%A1-la-go-use-an-agent-noun-based-on-the-verb-that-captures-the-behaviour-raw-fetcher-endraw-raw-jsonparser-endraw-etc)。如果你的界面描述的是行为(á la Go)，那么根据描述行为的动词使用[代理名词](https://en.wiktionary.org/wiki/agent_noun#English):`Fetcher`，`JSONParser`等等。

#### 4。名字的长度应该与变量的寿命和作用域成正比。如果只是迭代计数器，`i`就可以了。但是，如果它被导出，那么我想看到一点冗长。

##### [4.1](#41-this-matters-less-with-typed-languages-im-pretty-confident-i-know-what-raw-db-endraw-is-if-its-declared-as-raw-database-db-endraw-)。这对于类型化语言来说关系不大；如果声明为`Database db = ...`，我很有信心知道`db`是什么

#### 5。功能和方法是动作——适当地命名它们:`fetch`、`read`、`get`等。

##### [5.1](#51-name-methods-and-namespaced-functions-fluently-taking-into-account-the-likely-receiver-name-raw-filewrite-endraw-not-raw-filewritefile-endraw-)。流利地命名方法(和命名空间函数)，考虑(可能的)接收者名称；`file.write()`不是`file.writeFile()`。

#### 6。不要再这么努力了——你可能给它起了个错误的名字，所以明天你有时间思考的时候再回来。

* * *

*编辑:删除了关于常数的尖叫蛇案例的第 3 点，因为我已经确信这是毫无意义的*

*编辑:我只想指出这些是我自己想出来的，所以如果有人在六个月后的代码评审中引用它们，我会很讨厌的*

有哪一个工具/堆栈/框架尽管很流行，但你却不感兴趣？ [@cjbrooks12](https://dev.to/cjbrooks12) 谈到他们对最流行的框架之一缺乏兴趣:

[![cjbrooks12 profile image](img/97fbb987cec86db9d610eae97c0dfa7b.png) ](/cjbrooks12) [ Casey Brooks ](/cjbrooks12) • [<time datetime="2019-06-08T17:17:12Z" class="date-short-year"> Jun 8 '19 </time>](https://dev.to/cjbrooks12/comment/bigk) 

做出反应。我通常不喜欢 JavaScript，但是如果我要使用它，我会选择 Vue 或者 vanilla。我无法越过所有需要的东西(webpack、redux、es6 等)的巨大障碍。)变得更有效率。这个社区试图把所有东西都变成 React，从外面看起来就像是对 React 的滥用。

最后， [@rixcy](https://dev.to/rixcy) 提供了一个很棒的资源列表来回应 **[网页开发者和设计师们，你们的原材料从哪里来？](https://dev.to/prahladyeri/web-developers-and-designers-where-do-you-get-your-raw-materials-from-14pk)** :

[![rixcy profile image](img/a63d15e58a26306c2541eaaaad6690b0.png) ](/rixcy) [ Rick Booth ](/rixcy) • [<time datetime="2019-06-06T10:11:57Z" class="date-short-year"> Jun 6 '19 </time> • Edited on <time datetime="2019-06-06T10:13:02Z" class="hidden m:inline-block date-no-year">Jun 6</time>](https://dev.to/rixcy/comment/bg7h) 

*   【logos 如果我需要快速制作原型的东西，可以使用 Logodust 。

*   [Undraw](https://undraw.co/) 为开源 svg 插图

*   用于可重复模式的[英雄模式](http://www.heropatterns.com/)、[模式库](http://thepatternlibrary.com)和[创新模式](https://www.novapattern.com)

*   [纸杯蛋糕 Ipsum](http://www.cupcakeipsum.com/) ，[培根 Ipsum](https://baconipsum.com/) ，[猫咪 Ipsum](http://www.catipsum.com/) 为了更好的 Lorem Ipsum text

*   [templated.co](https://templated.co/)获取免费模板/灵感

*   UIGoodies 上面也有不少像样的东西。

*   [cssfx](https://cssfx.dev/) 用于快速复制粘贴 css 动画

下周见，更多精彩评论，✌
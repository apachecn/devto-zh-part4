# 更懒于语义 HTML

> 原文：<https://dev.to/wtaylor45/be-lazier-with-semantic-html-3mge>

<figcaption>Cover photo by Drew Coffman on Unsplash</figcaption>

* * *

我知道语义 HTML 已经是旧闻了，但是我在这里大声疾呼，使用它比不使用它更容易。如果你遇到仇恨者，把这个给他们看

* * *

你可能听说过“Div 汤”这个词。这时，一个网站大部分是由数百个 div 组成的，这些 div 通常相互嵌套。如果你想要一个好的例子，看看 Twitter 的来源。

div 汤怎么了？嗯，主要有三个问题。

1.  网络爬虫将有一个更糟糕的时间索引和排名你的网站
2.  你的代码库更难通读和理解
3.  更难考虑和实现可访问性

现在，我并不想攻击 Twitter。他们显然没有 SEO 问题，从可访问性的角度来看，该网站运行良好，我只是假设他们有一个维护良好的代码库。这应该归功于所有努力重新设计并把它推出的人。

然而，我在这里倡导一种不同的方式来对待 Twitter 付出的努力。这种方法是...**懒惰**。

## 语义 HTML

对于那些不完全熟悉我所说的“语义 HTML”的人，让我简单介绍一下。

HTML5 中发布的语义 HTML 元素(以及 4 中的一些元素)赋予页面内容以意义，与任何样式决策无关。例如，`section`元素在技术上可以用在任何使用`div`的地方。然而，这不是`section`元素的重点。它是一种将主题相关的内容组合在一起的方式。元素本身对站点的视觉显示方式没有影响。

还有很多其他的元素，我强烈建议你去看看这篇文章，这篇文章会给你一些细节。

那么这和懒惰有什么关系呢？

它之所以懒惰，是因为创建或更新一个使用语义元素的站点不需要您迁移到一个新的库或框架。它不需要一些高度专业化的知识，也不需要在此基础上对你的 CSS 进行全面重构。它之所以懒惰，是因为当开发者试图让使用屏幕阅读器的人访问你的网站时，它可以减轻他们的负担。

它很懒，但是仍然有很多优点。让我们再来看看之前关于 div soup 的痛点列表。

### SEO

当一个网络爬虫来决定它应该如何排列你的站点时，它使用许多不同的策略来理解页面上发生了什么。最基本的策略之一是使用 HTML 标记名。

例如，让你的标题、导航条和页脚清楚地标注上相应的 HTML 元素，并在`main`标签下标注你的主站点内容，这样爬虫就能快速识别出它应该在哪里寻找你站点的真正内容。这与使用更多知名的 SEO 标签如`title`和所有的`meta`变体是同样的想法。

SEO 是一种黑箱。没有具体的方法说“是的！使用 HTML5 元素会让你的排名大幅上升！”。然而，众所周知，网络爬虫确实使用 HTML 标签来尝试并找出在哪里寻找。如果你能让他们的生活变得更容易，那就很有可能帮助你的网站。

### 更难读懂的代码库

一个可维护的代码库是非常非常难以实现的。这就是为什么像 ESLint 和 Prettier 这样的工具如此重要。你可以随意格式化你的代码，但是如果所有的东西和它的狗都是一个`div`，在你明白你在看什么之前，你可能会很头疼。

看看这美景。

```
<div class="header">
    <h1>Hello There</h1>
</div>
<div class="container">
    This is some of the more important content. This is a basic intro to it.
    <div>
        Now let's get into the content
        <div>
            <p>This is an example piece of content</p>
        </div>
        <div>
            <p>This is an example piece of content too</p>
        </div>
    </div>
</div>
<div>
    This is my footer.
    Here's some legal info.
</div> 
```

一旦你掌握了节奏，解析起来并不复杂。然而，乍一看，这真的很难理解。几乎所有内容都用一个`div`分隔开，我们唯一的其他线索是类名和内容本身。

所以让我们尽可能用最懒惰的方式来解决这个问题。让我们把元素上的标签名改成语义上的。

```
<header class="header">
    <h1>Hello There</h1>
    <h2>This is the header!</h2>
</header>
<main class="container">
    This is some of the more important content. This is a basic intro to it.
    <section>
        Now let's get into the content
        <article>
            <p>This is an example piece of content</p>
        </article>
        <article>
            <p>This is an example piece of content too</p>
        </article>
    </section>
</main>
<footer>
    This is my footer.
    <small>Here's some legal info.</small>
</footer> 
```

只做了一些小改动，这一页就变得更容易阅读了。没有库，没有奇怪的不合时宜的评论，只有纯 HTML。另一个好处是页面看起来和以前完全一样(除了`small`元素，它改变了字体大小)😬).

请记住，对于这种小规模的例子来说，这可能无关紧要，但是随着站点的增长，利用语义元素所获得的价值也会增长。即使当你把你的网站分解成更小的组件时，也是如此。

### 可达性

可访问性是每个开发人员都需要注意的。相当一部分用户有残疾，在浏览网页时使用某种辅助技术。

当谈到使你的网站具有可访问性时，有很多要考虑的，(太多了，这里不一一介绍)。幸运的是，你可以通过使用语义 HTML 来摆脱一点点懒惰。

免责声明:我并不是说这就是你为实现可访问性所需要做的一切。它只是有帮助。

为了说明使用语义 HTML 的一大优势，让我们来看看早期的[可访问性树](https://developers.google.com/web/fundamentals/accessibility/semantics-builtin/the-accessibility-tree)中的代码。可访问性树是 DOM 的一种表示，屏幕阅读器用它来向用户阅读。

这是非语义 HTML 的一部分的树:

[![accessibility tree showing only a paragraph tag and some text within it](img/0a28efcf075ca8b5ab7268016bf34512.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q8DSCZd3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e58v5q8roqakfwar8p5o.png)

好吧，这很有道理。这是一段文字。那么，使用语义元素的可访问性树呢？这是与上面完全相同部分的树:

<figure>

[![accessibility tree showing main, section, article and paragraph tags](img/2c4d48c46386cd4ebea13ff99418d3ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UF2Gl4yh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/enjvk4h6t3z466r1mnhb.png)

<figcaption>No, that's not just an un-cropped version of the first picture.</figcaption>

</figure>

那里其实还有很多！关于屏幕上呈现的内容和用户到底在读什么，有更多的上下文。

为什么不同？

可访问性树将省去大多数 div，因为它们的目的通常是风格上的，而不是语义上的。因此，屏幕阅读器没有理由关心它们。

你可以并且**应该**在两个例子中添加 aria-label 来帮助屏幕阅读器。这样做会将 div 和标签一起包含在可访问性树中。不过，我们谈论的是我们在这里能获得的最懒的胜利。

*更新:这里有一段来自 [MDN 的咏叹调文档](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA)T3 的引用*

> 这些小部件中的许多后来被合并到 HTML5 中，
> 开发者应该更喜欢使用正确的语义 HTML 元素
> 而不是 ARIA，如果这样的元素存在的话。

## 结论

语义 HTML 不是灵丹妙药，可以神奇地解决你的所有问题，治愈你的所有病痛。标签也不是魔鬼。它们有它们的用途，应该继续以适当的方式使用它们。然而，语义 HTML 是一种超级简单、几乎没有风险的方法，可以让你的网站轻松获胜。

因此，无论您是开始一个新项目，还是继续在一个单一的遗留代码库上工作，您都可以轻松地利用语义 HTML。

所以请吧。说真的。伙计们，这已经是正式的 5 年了。
# 你应该避免这 7 个 CSS 禁忌！☝️

> 原文：<https://dev.to/webdeasy/you-should-avoid-these-7-css-no-gos-2kj>

## *[更新版本可用(点击)！](https://webdeasy.de/en/css-tips/)🔥*

一遍又一遍地阅读 CSS 技巧和为了尽可能快地创建漂亮的网站必须考虑的事情。有时看到不好的例子有助于了解什么是真正好的。你可以在这里找到这些 CSS 禁忌！

***重要:**以下对“坏”CSS 的禁忌仅是我个人的看法。这些都不是固定的规则，只是我自己经验总结出来的小技巧！*

# 1。样式重置

每个 HTML 元素都有一些默认样式，这应该使元素独一无二。这也决定了元素的应用领域或用途。因此，您应该使用这些默认样式，而不是重置它们。使用标签 *p* 的例子可以很好地解释这个问题。

```
/* No-Go */

/* Default Style */
p {
  display: block;
  margin-top: 1em;
  margin-bottom: 1em;
  margin-left: 0;
  margin-right: 0;
}

/* Wrong: Our custom Style */
p {
  display: inline-block;
  margin: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将覆盖默认的*显示*和*边距*属性。这里一个 *span* 元素更合适，因为默认情况下它没有样式。

这样会更好:

```
/* Correct: Our custom Style */
span {
  display: inline-block;
  margin: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，我们必须适应，有时会覆盖许多元素的样式，因为否则它们将与 CD(公司设计)不匹配，并且每个页面看起来都一样。但是可以稍微注意一下。这节省了 CSS 代码，从而在加载页面时节省了重要的时间。

在这里你可以找到所有 HTML 标签的默认样式列表。

# 2。不合适的类名

如果您想让其他开发人员定制代码，这种禁忌非常重要。类似于精心选择的注释，类名对于可读代码来说是重要的一步。

关于这一点，我们必须区分三个错误。

## 2.1 太短——无意义

如果类名太短，通常就没有意义。我们能用 f 做什么？对:没事！因此，总是选择一个有意义的类名，并尝试使用尽可能多的辅助类。你不应该那样做:

```
/* No-Go */

.f {
  color: red;
}
.a {
  margin: 1rem 3rem 2rem 3rem;
}
.cf {
  display: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是有意义的助手类的样子:

```
.red-color {
  color: red;
}
.margin-small-big {
  margin: 1rem 3rem 2rem 3rem;
}
.display-none {
  display: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 2.2 与 HTML 标签相同

这些类名可以理解，但完全是多余的。我们已经可以通过元素名称部分或 span 选择 HTML 元素，不需要额外的同名类。以下是一个禁忌:

```
/* No-Go */

section.section {
  min-height: 80vh;
}
span.span {
  color: red;
  font-size: 12px;
} 
```

Enter fullscreen mode Exit fullscreen mode

那就更好了:

```
section {
  min-height: 80vh;
}
span {
  color: red;
  font-size: 12px;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 2.3 令人困惑

混乱的类名是最糟糕的。如果我在代码中读到 no-margin，我还假设 CSS 中的 margin 设置为零。同样，对于颜色-红色，每个人都假设背景色或字体颜色设置为红色。它不应该是这样的:

```
/* No-Go */

p.color-red {
  color: green;
}
.no-margin {
  margin: 3rem 0.5rem;
} 
```

Enter fullscreen mode Exit fullscreen mode

不会引起混淆的类名可以是这样的:

```
p.color-green {
  color: green;
}
.margin-big-small {
  margin: 3rem 0.5rem;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 3。重要关键字

我们都知道:不能改变的风格，因为它经常被覆盖或来自插件。然后我们就喜欢用这个小魔字*！重要的*在我们的 CSS 中。

它导致这种风格总是适用，问题应该是固定的。但这是恶性循环的开始。下一次你想改变它而又懒得再搜索的时候，你再次使用这个关键词，有时会出现完全的混乱。

因此，在使用关键字之前，你应该好好思考和搜索。有时候是不可避免的，但更多时候是可以用不同的方式解决的。

这就是我不想这么做的原因:

```
/* No-Go */

span.foobar {
  color: red!important;
  margin-top: 4rem!important;
  font-size: 12px!important;
  border-bottom: 1px solid #000!important;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 4。空选择器

在所有 CSS 禁忌中，这可能是最不重要的，但同时也是最容易避免的禁忌。

我们说的是空的选择器。换句话说，不包含任何属性的选择器。这些选择器总是丢失几个字节的内存。

如果这经常发生在代码中，并且我们有很多大的 CSS 文件，它会影响页面的加载时间。

这就是为什么 CSS 中没有丢失这样的东西:

```
/* No-Go */

p { }
span { }
.no-margin { }
footer { }
footer > a { } 
```

Enter fullscreen mode Exit fullscreen mode

# 5。覆盖样式

我们已经讨论过关键词了！重要”。这允许我们覆盖样式，以便 CSS 规则总是适用。如果我们太频繁地使用它，我们在以后的改变中会有很大的问题。但是，即使没有关键字，我们也应该尽量避免覆盖。

你应该避免:

```
/* No-Go*/

p {
  font-size: 2em;
  color: #FFF;
}

/* ..other code.. */

p {
  font-size: 2.2em;
  line-height: 20px;
}

/* ..other code.. */

p {
  font-size: 1.8em;
} 
```

Enter fullscreen mode Exit fullscreen mode

但是你可以很容易地避免这个错误。您可以简单地在 CSS 文件中搜索 CTRL + F 并相应地更改它，而不是总是添加新的样式。

这节省了我们的代码，因此存储空间和文件也保持清晰。

它可能是这样的:

```
p {
  font-size: 1.8em;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 6。太多的绝对定位

我喜欢绝对定位。在许多情况下，这确实很有帮助，而且在某些情况下，这是不可避免的，也是有益的。

但是，其实还是有人把每一个元素绝对定位的。有许多理由你不应该做那件事。一方面，逐个像素地定位每个元素要困难得多。大量的文书工作，大量的试验和错误，它看起来不干净。

让页面具有响应性也要困难得多，你必须插入许多断点才能让页面在任何分辨率下都好看。为什么要让你的生活比现在更艰难…？但是就像我说的:绝对定位也有很多很好的应用。

# 7。CSS 太多

我看到的一个问题，例如预制的主题。你已经有很多 CSS 代码(也有很多不必要的代码)，然后你甚至添加自己的代码。所以混乱结束了。

这是许多被列为禁忌的地方。覆盖、空选择器、频繁使用“！最重要的是:令人困惑！因此，您应该直接删除或重写所有不需要的或您更改的代码。

但是对于其他项目，你应该写最少的代码。它让许多事情变得更容易，极简主义现在是现代和美丽的。

# 结论

所以你可以考虑很多因素来编写清晰的、高性能的 CSS。你永远不会停止学习，你总是可以提高你的“代码”。我也知道你不能总是避免这些 CSS 禁忌，但你可以总是努力提高自己。

如果你正在寻找 JavaScript 挑战，请看看这个(抱歉小广告):

## 文章不再可用

感谢阅读！如果你喜欢这篇文章，请让我知道并分享它！如果你愿意，你可以看看我的博客，在 T2 的推特上关注我！😊
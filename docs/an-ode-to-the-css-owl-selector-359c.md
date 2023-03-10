# CSS 猫头鹰选择器的颂歌

> 原文：<https://dev.to/kevtiq/an-ode-to-the-css-owl-selector-359c>

我喜欢 CSS 不是秘密。几年前，我爱上了一个非常简单，但功能强大的 CSS 选择器。那时我正在把我的 CSS 扩展到下一个层次。我知道特异性和级联。从头开始使用 CSS 或者使用框架对我来说都没有问题。但是我遇到了新的复杂解决方案的 CSS 选择器。所以我必须扩展我的 CSS 知识。

我从网上找资源开始，像[砸杂志](https://smashingmagazine.com/)。在那里，我遇到了[海登·皮克林](https://twitter.com/heydonworks)，以及后来他的[“脑叶切除猫头鹰选择器”](https://alistapart.com/article/axiomatic-css-and-lobotomized-owls/)。这个选择器让我大吃一惊。在 [CSS 日](https://cssday.nl/2019)他甚至还秀出了另一种美，叫做[‘flexbox 圣信天翁’](http://www.heydonworks.com/article/the-flexbox-holy-albatross)(这里可以看[)。这些类型的解决方案向我展示了 CSS 比我知道的要强大得多。用 CSS 解决问题可以很简单，也可以很优雅。所以，Heydon，这(部分)是给你的。](https://www.youtube.com/watch?v=RUyNJaoJH_k)

> "用 CSS 解决问题可能很简单，也可能很优雅."

## 脑叶切除的猫头鹰选择器

海登在他的[文章](https://alistapart.com/article/axiomatic-css-and-lobotomized-owls/)中比我更好地解释了选择器。但是我将提供一个简短的总结。如前所述，选择器非常简单:`* + *`。`*`是 CSS 中的通用选择器，它适用于 DOM 中的所有元素。这个`+`是这段 CSS 代码的真正英雄。它有一个美丽的名字[‘相邻兄弟组合子’](https://developer.mozilla.org/en-US/docs/Web/CSS/Adjacent_sibling_combinator)。如果第一个*元素紧跟在第二个*元素之后，它会将定义的样式应用于第二个*元素。使用我们的选择器，它将样式应用于 de DOM 中同一级别的所有非第一元素。除非其他规则有更高的[特异性](https://css-tricks.com/specifics-on-css-specificity/)。* 

```
* + * { 
  margin-top: 1.5rem;
} 
```

Enter fullscreen mode Exit fullscreen mode

那么为什么这个选择器如此强大呢？我在为博客网站搜索间距解决方案时找到了选择器。我想在段落之间创造足够的空间，同时也给它们的父元素创造足够的空间。存在许多解决方案来解决这个问题。你可以给每个元素一个`margin-bottom`。这对最后一个元素有副作用。要解决这个问题，用`:last-of-type`伪选择器覆盖样式。另一个解决方案是在每个段落中添加一个`padding-top`和`padding-bottom`。这可能会对父元素的填充产生不必要的副作用。关于这个具体问题和解决方案的更深入的描述可以在 [Every Layout](https://every-layout.dev/layouts/stack/) 上找到，这是一个很棒的网站。

当使用`margin-top`时，`* + *`似乎是一个优雅的解决方案。`margin-top`只适用于元素之间。但是您也可以使用具有不同间距的`img`或`svg`元素来生成图像。然后试试`p + p`之类的。您可以根据需要将这种结构具体化，确保解决方案简单而优雅。但你知道是什么让它如此强大吗？在这个设置中，它作用于嵌套元素！

[![The owl selector applied](img/8270c722476f31c7daa16ded715dd849.png "The owl selector applied")](https://res.cloudinary.com/practicaldev/image/fetch/s--0i-wfCJb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kevtiq.co/img/owl-layout.png)

正如您在左边看到的，`margin-top`规则适用于列表中的每个元素。在右边，第二个元素不仅获得样式规则，而且还有两个子元素。在这些子元素中，第二个元素也有边距。

## UI 中的算法

你可能想知道为什么我对这个小小的 CSS 选择器如此着迷？在 web 开发行业，在 2019 CSS 日，有一个反复出现的主题。CSS 是编程语言吗？关于这个问题的答案，我推荐[劳拉·申克](https://twitter.com/laras126)的演讲，你可以在这里找到[。这个演讲让我思考 CSS 的现状以及我自己如何使用它。浏览器将我的 CSS 命令转换成屏幕上显示的东西有多复杂？](https://www.youtube.com/watch?v=dtddBM8s7xY)

owl 选择器是一个很好的例子，展示了解析 CSS 选择器有多复杂。它显示了 CSS 的真正威力。为什么？因为它是嵌套工作的。在任何编程语言中，这都可能很快变得复杂。选择器的嵌套性质可以与[递归](https://en.wikipedia.org/wiki/Recursion_(computer_science))相媲美。在深入研究递归解决方案之前，让我们看一下元素平面列表的伪代码。

*免责声明:所使用的代码示例不适用于任何编程语言，它们是伪代码片段。*

```
function owl(list, apply) {
  for (i = 1; i < list.length; i++) {
    apply(list[i]);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的函数得到一个元素列表和一个`apply`函数作为输入。如你所见，我们从`1`开始。不是因为数组从 1 开始(事实并非如此)，而是因为我们的 CSS 选择器默认跳过了第一个元素。该函数的工作方式类似于非嵌套列表中的`* + *`。回调`apply`用于列表中的任何元素。但是如果我们想要像`p + p`甚至`img + p`那样的东西呢？我们必须添加检查以确保相邻元素遵循定义。

```
function isFirst(item) { ... }
function isSecond(item) { ... }

function owl(list, apply) {
  for (i = 1; i < list.length; i++) {
      if (isFirst(list[i]) && isSecond(list[i - 1)) {
        apply(list[i]);
      }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用函数，我们可以处理不同类型元素的列表，并检查相邻元素是否符合我们的标准。我们只缺少 CSS 选择器的嵌套功能。我们可以检查我们的元素是否有子元素。如果是，我们再次调用`owl`函数。然而，在 HTML 中，任何元素都可以有子元素。这意味着，即使我们的元素符合我们的相邻规则，也可以有子元素。因此，如果我们的元素有子元素，我们不需要调用`owl`函数，而是首先调用`apply`函数。因此，对于单个元素来说，可能会同时调用`apply`和`owl`。

```
function isFirst(item) { ... }
function isSecond(item) { ... }
function hasChildren(item) { ... }

function owl(list, apply) {
  for (i = 1; i < list.length; i++) {
    if (isFirst(list[i]) && isSecond(list[i - 1)) {
      apply(list[i]);
    }
    if (hasChildren(list[i]) {
        owl(list[i], apply);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

您现在知道如何创建类似于我们的 owl 选择器的东西了。它是一个递归函数，带有一些额外的函数来检查条件。如果我们的 CSS 变得更复杂，上面的伪代码可以变得更复杂。尝试将其与不同的伪选择器组合，或者改变其特异性。通过这样做，您将会看到 CSS 变得多么强大。

## CSS 是编程语言吗？

正如我提到的，在 CSS 时代，一个反复出现的话题是“CSS 是一种编程语言吗？”。几乎每个人都可以应用简单的 CSS 规则或样式规则。解决更复杂(甚至简单)的问题需要更深入的知识。计算机科学概念的知识变得很重要，因为它们是 CSS 规则的结果。

> "计算机科学概念的知识变得很重要，因为它们是 CSS 规则的结果."
> <cite>[凯文·彭尼坎普](https://kevtiq.co)T4】</cite>

一个简单的 CSS 选择器意味着你要应用一个递归函数。这无非就是用了别人写的函数。结果的心理模型保持不变。您正在应用算法来创建 UI。这正是我热爱 CSS 的原因。一些简单的东西可以成为一个强大的 UI 操作工具。每次我在 CSS 中找到我认为不可能的解决方案。所以我想感谢 Heydon Pickering，Lara Schenck 和所有其他人，他们向我展示了 CSS 的真正力量。

*本文原载于[kevtiq.co](https://kevtiq.co/blog/an-ode-to-the-css-owl-selector/)T3】*
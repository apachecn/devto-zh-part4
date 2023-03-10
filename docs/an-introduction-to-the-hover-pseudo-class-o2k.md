# Hover 伪类简介

> 原文：<https://dev.to/laurieontech/an-introduction-to-the-hover-pseudo-class-o2k>

在我们深入 hover 之前，让我们先了解一些基础知识。什么是伪类？

首先，伪类是一个跟在选择器后面的关键字，出现在冒号(`:`)之后。类似这样的。

```
h1:active {
  color: blue
} 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，伪类是`active`。它描述了给定元素的状态。当元素处于该状态时，将应用关联的样式规则。

有许多伪类，都在这里列出[。](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes#Index_of_standard_pseudo-classes)

今天我们要讲一个具体的伪类，`hover`。

### 我们的按钮

让我们从一个按钮开始。我们把它设计得漂亮、大而易读。

[https://codepen.io/laurieontech/embed/pozERYY?height=600&default-tab=result&embed-version=2](https://codepen.io/laurieontech/embed/pozERYY?height=600&default-tab=result&embed-version=2)

现在，它是一个很棒的[语义](https://developer.mozilla.org/en-US/docs/Glossary/Semantics#Semantics_in_HTML)按钮，但是它是平的。如果我们点击它，什么都不会改变。没有直观的迹象表明它可以被点击！

那么我们该如何改变这种情况呢？

### 还是用 JavaScript 吧！

很长一段时间，为了创建这样的动态行为，您使用 JavaScript。让我们看一个例子。

该代码笔向我们的按钮添加了一个`mouseover`事件，并在这种情况下应用了样式。`mouseover`就像它听起来一样，当鼠标在元素上时，代码被触发。这个例子还包括一个`mouseout`事件，当光标不再位于元素上时，该事件重置原始样式。

[https://codepen.io/laurieontech/embed/ZEzpLZd?height=600&default-tab=result&embed-version=2](https://codepen.io/laurieontech/embed/ZEzpLZd?height=600&default-tab=result&embed-version=2)

### 悬停

在我们的 JavaScript 示例中，`mouseover`和`mouseout`函数包含了改变样式的逻辑。但我们只做了这些。我们不应该为此需要 JavaScript！

事实证明，我们没有。我们可以用 CSS 伪类实现同样的功能。

[https://codepen.io/laurieontech/embed/PoYNQQW?height=600&default-tab=result&embed-version=2](https://codepen.io/laurieontech/embed/PoYNQQW?height=600&default-tab=result&embed-version=2)

这支笔使用的是`hover`伪类。`hover`当用户与一个元素交互但尚未激活它时触发。这与我们之前使用的`mouseover`功能几乎相同。

此外，只要`button:hover` CSS 样式不同于为通用按钮定义的样式，我们根本不需要`mouseout`。`hover`只有当元素处于该状态时才会被触发，否则，那些样式不会出现。不需要像在 JavaScript 示例中那样“关闭它们”。

> 如果您需要以不同的方式考虑这个问题，就会触发 JavaScript 事件并应用样式规则，直到您用新规则取代它们。在 CSS 伪类的情况下，只有当元素处于这种状态时，规则才适用，否则样式不再出现。

### 好玩的例子

我们上面做的并不是特别时髦的 CSS。我们只添加了几个规则来反转鼠标悬停时按钮的颜色。这里有一个更有趣的例子，你可以用 hover 做一些事情！

[https://codepen.io/laurieontech/embed/ExYgWQZ?height=600&default-tab=result&embed-version=2](https://codepen.io/laurieontech/embed/ExYgWQZ?height=600&default-tab=result&embed-version=2)

### 结论

现在你知道了。下一次，当您希望根据元素的交互状态来改变元素时，请查看一些伪类，看看是否能在那里找到您想要的东西。能使用 CSS 和 HTML 的地方就用 CSS 和 HTML，真是妙不可言！而且是[广泛支持](https://caniuse.com/#feat=css-sel2)！
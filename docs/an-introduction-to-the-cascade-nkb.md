# 级联简介

> 原文：<https://dev.to/colabottles/an-introduction-to-the-cascade-nkb>

这只是一个压缩形式的介绍。只是对级联的一个小的基本概述。

[CSS 级联 4 级规范声明](https://www.w3.org/TR/css-cascade-4/#cascading)；

> cascade 接受给定元素的给定属性的声明值的无序列表，按照声明的优先级对它们进行排序，并输出单个级联值。

选择器的优先级是有顺序的。重要性、范围、特殊性和顺序。让我们从重要性开始检查每个优先级。你不必一下子全明白。分解每一部分，直到你理解它，然后继续下一部分。我已经提供了代码示例。

### 重要性

一个`!important`声明优先于普通声明。例如，当星形选择器用于站点范围的样式时，举个例子`box-sizing: border-box;`，然后同一个属性(在下面的例子中是`background-color`)在`body`选择器的情况下使用它。所以`background-color: red !important;`会否决`background-color: white;`。

```
* { background-color: white;}body { background-color: red !important;} 
```

Enter fullscreen mode Exit fullscreen mode

*   转换声明`transition-timing-function: steps(6, end);`或`transition: all .25s ease-in-out;`

*   重要用户代理声明

```
input, textarea, keygen, select, button, meter, progress { -webkit-writing-mode: horizontal-tb !important;} 
```

Enter fullscreen mode Exit fullscreen mode

*   重要用户声明`p { text-indent: 1em !important; }`

*   重要作者声明`p { text-indent: 1.5em !important; }`

*   动画声明

```
@keyframes slide-right { 50% { margin-left: 110px; opacity: 0.9; } to { margin-left: 200px; }} 
```

Enter fullscreen mode Exit fullscreen mode

*   普通作者声明`p { text-indent: 1.5em; }`

*   普通用户声明`p { text-indent: 1em; }`

*   普通用户代理声明`keygen, select { border-radius: 5px; }`

在这里阅读更多关于[转换声明](https://www.w3.org/TR/css-cascade-4/#biblio-css-transitions-1)的内容。

在此阅读更多关于[动画宣言](https://www.w3.org/TR/css-cascade-4/#biblio-css-animations-1)的内容。

### 范围

声明的作用域可以是一个子树，这样它只影响它的作用域元素和该元素的后代。一个例子是`html`到`body`。

> 如果两个声明的作用域元素具有祖先/后代关系，那么对于普通规则，其作用域元素是后代的声明获胜，对于重要规则，其作用域元素是祖先的声明获胜。

换句话说，对于普通的声明，内部作用域的声明被覆盖，但是对于`!important`规则，外部作用域的声明被覆盖。晚赢早。

```
h1 { color: red;}/* This one wins */h1 { color: green;}

/* This one wins due to specificity */.title__heading { font-size: 2em;}h1 { font-size: 2.8em;} 
```

Enter fullscreen mode Exit fullscreen mode

> 当考虑所有这些级联理论，以及什么样式应用于其他样式时，您应该记住的一件事是，所有这些都发生在属性级别——属性覆盖其他属性，但您不会得到覆盖其他规则的整个规则。当多个 CSS 规则匹配同一个元素时，它们都应用于该元素。只有在这之后，才评估任何冲突的属性，以查看哪个单独的样式将胜过其他样式。

[一个不在规则上的混合](https://developer.mozilla.org/en-US/docs/Learn/CSS/Introduction_to_CSS/Cascade_and_inheritance#A_note_on_rule_mixing)

### 特异性

*   内嵌样式(在`<style></style>`标签之间的任何东西)
*   ID 选择器
*   类/伪选择器
*   类型选择器(例如，`h1` ) &伪元素(`::before`)

### 订单

文档顺序中的最后一个声明胜出。加载样式表的顺序很重要。如果在 HTML 文档的头部链接了两个样式表，第二个样式表将覆盖第一个样式表中的规则。这就是为什么在你使用的主样式表中，resets 和`@imports`在其他任何东西之前被加载。

### 总结

我见过很多人因为不理解 CSS 而沮丧。有些人憎恨学习它，有些人鄙视它。了解级联有益于开发人员。从长远来看，对级联和级联如何工作的基本了解将使你受益。我给你一些建议。

该规范可能看起来令人望而生畏，该语言可能看起来很陌生，但归根结底，事实并非如此。如果我能通过阅读说明书得到它，那么任何人都可以得到它。有些杰出的人对 JavaScript 了如指掌。当谈到 CSS 和 cascade 时，他们说这是一个他们不能完全理解的概念。

你不需要一页一页、一个字一个字地了解整个规范。我已经这样做了 21 年多了，仍然需要查找资料。

我通过把事情分解到核心来学得最好。一块一块的。将级联分解成一点一滴(想想原子设计——原子、分子、有机体),并对其进行剖析，直到我完全掌握概念。

如果有任何我能回答的问题，请随时联系我。
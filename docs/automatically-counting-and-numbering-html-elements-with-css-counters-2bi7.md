# 用 CSS 计数器对 HTML 元素自动计数和编号

> 原文：<https://dev.to/wangonya/automatically-counting-and-numbering-html-elements-with-css-counters-2bi7>

假设你有一个未知数量的`<div>`元素，并且(出于某种原因)你想对它们进行计数，并自动对它们进行编号——你会怎么做？如果您像我一样，您的第一个想法将是使用某种 Javascript 来处理 DOM。如果我告诉你(在这里插入 Morpheus 的声音)你可以用 CSS 做到这一点呢？

## 如何使用 CSS 计数器

让计数器工作需要三个步骤:

*   用`counter-reset`初始化计数器
*   用`counter-increment`增加计数器
*   用`counter()`显示当前计数器值

很简单，真的。下面的笔演示了如何计算和编号三个`<div>`元素。

[https://codepen.io/wang0nya/embed/voRyaJ?height=600&default-tab=result&embed-version=2](https://codepen.io/wang0nya/embed/voRyaJ?height=600&default-tab=result&embed-version=2)

## 这在哪里会有用？

我在寻找一种给博客文章中的图片编号的方法时发现了 CSS 计数器。这是它真正派上用场的一个地方。这里有一个简单的例子:

[https://codepen.io/wang0nya/embed/LwdxZz?height=600&default-tab=result&embed-version=2](https://codepen.io/wang0nya/embed/LwdxZz?height=600&default-tab=result&embed-version=2)
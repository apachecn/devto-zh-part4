# 用超光速粒子和功能性 CSS 在 10 天内完全重写:一个案例研究(第四部分)

> 原文：<https://dev.to/simonswiss/full-re-write-in-10-days-with-tachyons-and-functional-css-a-case-study-part-4-4b11>

### 这是 4 篇文章系列的最后一章，带你通过使用[超光速粒子](https://tachyons.io)完成一个网站重构过程。

### 菜单上的

1.  [当初为什么要重写？](https://dev.to/simonswiss/full-re-write-in-10-days-with-tachyons-and-functional-css-a-case-study-part-1-3dkm)
2.  什么是函数式 CSS？
3.  [一步一步的重构过程](https://dev.to/simonswiss/full-re-write-in-10-days-with-tachyons-and-functional-css-a-case-study-part-3-1k4)
4.  **后果:重要的学习和建议**(你在这里！👋)

* * *

## 第 4 章:后果——主要经验和建议

在这最后一篇文章中，我试着分享一些我在重构过程中学到的东西。我在使用函数式 CSS 和在这个特定的生态系统中所学到的东西。

我将从解决人们似乎经常遇到的几个疑问或痛点开始。

* * *

## 1。你如何处理重复的类和铺天盖地的“丑陋的”HTML 标记？

没有人喜欢一遍又一遍地输入相同的类，或者到处复制/粘贴东西。除了费时，它的反面是干燥。当你决定对你的 UI 做一些全局的改变时，这会让你陷入痛苦的境地。

### 模板抽象拯救。

当使用函数式 CSS 时，我强烈建议您在工作流程中引入某种模板语言。这有着天壤之别。

一旦你发现一个重复的课堂模式，相信我，你会的！-把它抽象成一个变量。我喜欢把这个过程称为“**类组合**助手的创建。

### 演示给我看！

让我们直接从 [tachyons.io](http://tachyons.io/) 组件库部分获取一些代码。

这个组件是一个“联系人列表”,看起来像这样:

[![Contact list UI component from the Tachyons.io website](img/3f656b67cc6d4295c9214e7d0b23d306.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e9IgLTyn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y36eo0fnjwfnucur5ojv.png)

<figcaption>Contact List from the tachyons component library</figcaption>

* * *

让我们看看原始的 HTML +超光速粒子标记。是的，对你来说可能很难看:
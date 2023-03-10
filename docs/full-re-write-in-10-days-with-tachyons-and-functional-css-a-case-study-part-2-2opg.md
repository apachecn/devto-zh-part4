# 用超光速粒子和功能性 CSS 在 10 天内完全重写:一个案例研究(第二部分)

> 原文：<https://dev.to/simonswiss/full-re-write-in-10-days-with-tachyons-and-functional-css-a-case-study-part-2-2opg>

### 这是一个 4 篇文章系列的第二章，带你经历一个使用 tachyons 的网站重构过程，在这个过程中，我们看看什么是功能 CSS，为什么它会有用。

### 菜单上的

1.  [当初为什么要重写？](https://dev.to/simonswiss/full-re-write-in-10-days-with-tachyons-and-functional-css-a-case-study-part-1-3dkm)
2.  什么是函数式 CSS？(你来了！👋)
3.  [一步一步的重构过程](https://dev.to/simonswiss/full-re-write-in-10-days-with-tachyons-and-functional-css-a-case-study-part-3-1k4)
4.  [后果:主要经验和建议](https://dev.to/simonswiss/full-re-write-in-10-days-with-tachyons-and-functional-css-a-case-study-part-4-4b11)

* * *

## 第二章:什么是函数式 CSS？

那么，这是怎么回事？功能性 CSS 到底是什么意思？

你可能也听说过*原子* CSS 或者*不可变* CSS。这些指的是同一个东西。如果你熟悉函数式编程中的[纯函数](https://drboolean.gitbooks.io/mostly-adequate-guide-old/content/ch3.html)，你应该能很快掌握这个概念。

* * *

> 纯函数是这样的函数，给定相同的输入，将总是返回相同的输出，并且没有任何可观察到的副作用。

布尔博士，函数式编程的基本指南

* * *

把它想象成把你的 CSS 分成小的，*“只做一件事，并且做好它”*类。想想乐高积木。将这些类组合在一起，你可以创建任何你想要的风格。

这种完全不同的编写 CSS 的方法正在挑战许多已有的最佳实践。不会是一见钟情。

乍一看，这确实是一个可怕的想法。

## 一个基本例子

假设你想创建一个红色背景的警告框，一些填充和白色文本。

### “传统”方式

你可能会想写一些像这样的 HTML:
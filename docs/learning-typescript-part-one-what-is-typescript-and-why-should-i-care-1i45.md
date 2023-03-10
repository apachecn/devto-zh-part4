# 学习 TypeScript，第一部分:什么是 TypeScript，我为什么要关心它？

> 原文：<https://dev.to/dylanesque/learning-typescript-part-one-what-is-typescript-and-why-should-i-care-1i45>

我目前正在刷新我的 TypeScript 知识，并学习如何综合应用它来进行反应，所以你们都可以和我一起学习！今天，我们来看看 TypeScript 的基本“是什么”和“为什么”。

## 什么是 TypeScript？

TypeScript 是 JavaScript 的一个超集，它使开发人员能够在值上设置比普通 JS 更多的显式类型。这从原始值扩展到了对象、数组和一些自定义类型。如果你花时间学习过 Java 或 C#，你会对这种语法感到熟悉。它的目的是在开发的不同阶段指出并防止错误，并使代码更具可读性。

## 我为什么要在乎？

使 JavaScript 更容易入门的一个特性是，它是一种松散类型的语言，这意味着它可以从包含的数据类型中推断出值的类型。这使得最初学习语言比学习像 Java 这样的强类型语言要快，但是有一些[.......怪事](https://www.destroyallsoftware.com/talks/wat)。当这些奇怪的东西与我们人类经常容易出错的大脑发生碰撞时，就会产生问题。

套用 Kyle Simpson 的话来说，bug 生活在我们认为 JavaScript 如何工作和 JavaScript *实际工作方式*之间的距离所创造的空间中。缩小这一差距的一个方法是全面掌握 JavaScript 中的类型强制是如何工作的，并避免使用某些会导致错误的边缘值。这种方式工作量很大，对你来说可能值得，也可能不值得。另一种方法是使用类型系统，如 TypeScript 或 [Flow](https://flow.org/) 来增加程序的类型安全性。
# 类别与设计模式

> 原文：<https://dev.to/stereobooster/category-vs-design-pattern-483j>

关于面向对象编程(OOP)和函数式编程(FP)之间的区别有一个正在进行的讨论。我们来谈谈相似点。

在上一篇文章中，我们谈到了“原子”:对象和功能。在本帖中，我们来谈谈更大的构建模块。

## 什么是设计模式？

Alexander 等人将模式定义如下:

> 设计模式是由三部分组成的规则，它表达了特定上下文、问题和解决方案之间的关系。简而言之，这个模式同时也是一个东西，...，以及告诉我们如何创造那个东西，以及何时必须创造它的规则。
> 
> -一种模式语言:城镇、建筑、施工，1977 年

在 Erich Gamma 等人的书《设计模式:可重用面向对象软件的元素》之后，它们变得流行起来。

之前在这一领域已有工作，例如:

*   SmallTalk 引入了很多设计模式(包括著名的 MVC)
*   [Kent Beck 在 1987 年谈过模式](http://c2.com/doc/oopsla87.html)等。

如果我们遵循 Alexander 的定义，没有什么 OOP 是特定的(他们的书是关于架构的)。例如，算法和数据结构可以被认为是模式——如果你需要一个 O(1)访问的列表，你可以使用数组(或者向量),但是限制是...

目前，当人们说“设计模式”时，他们最有可能指的是 GoF 或类似的 OOP 模式。

有些人批评设计模式的想法。

## FP 中的模式是什么？

在 FP 中没有“更大的积木”意义上的“设计模式”的相同概念，我能想到的最接近的是范畴理论中的范畴。它不是更大的规模，因为功能本身可以被认为是一个范畴。

我不想深究范畴理论，所以我将给出简化的图片，这样我们可以做一个简单的比较。

> 范畴理论是描述数学中一般抽象结构的工具集。
> 
> 与集合论相反，范畴论并不关注 x,y,⋯称之为对象的元素，而是关注这些对象之间的关系...
> 
> ——[ncatlab.org](https://ncatlab.org/nlab/show/category+theory)

你应该对这个定义之后的范畴是什么有**近似**的理解，不是精确的。我们不需要精确，因为我们将把一个模式和一个函数进行比较，作为一个例子，就此打住。

对类别的典型批评(在编程中)是难以理解的。我所说的难以理解是指它们非常抽象，很难理解。

> 幺半群是内函子范畴中的幺半群，有什么问题？
> 
> ——来自[的笑话一部简短、不完整且大多错误的编程语言史](http://james-iry.blogspot.com/2009/05/brief-incomplete-and-mostly-wrong.html)

其他人可能会说，类别比设计模式更容易理解，因为它们有精确的定义(这是数学)。我从这个角度来看:我能够解释什么是模式(我希望如此)，但是对于范畴理论，我只能给出一点点解释。

## 策略 vs 功能

有一篇[大文章把设计模式比作类别](https://github.com/thma/LtuPatternFactory/blob/master/README.md)。如果你想知道更多细节，你可以看看。我们将讨论一个容易理解的例子。

### 策略模式 vs 功能(仿函数)

> 战略模式...是一种行为软件设计模式，支持在运行时选择算法。代码不是直接实现单个算法，而是接收运行时指令，以决定使用哪一组算法

在 OOP 中，这种模式将被实现为不同的对象(或类)，使用相同的方法:

```
// a11, a2
class AlphabeticCompare {
  compare(x, y) {
    /* ... */
  }
}

// a2, a11
class NaturalCompare {
  compare(x, y) {
    /* ... */
  }
} 
```

在 FP 中，不需要为此构造对象，相反，我们可以将函数作为值传递:

```
// a11, a2
const alphabeticCompare = (x, y) => {
  /* ... */
};

// a2, a11
const naturalCompare = (x, y) => {
  /* ... */
}; 
```

## 带走

我的想法是展示在两种范式中有一些相同的核心思想。
# 不是函数

> 原文：<https://dev.to/stereobooster/not-a-function-2a3g>

在[之前的文章](https://stereobooster.com/posts/not-a-function/)中，我们讨论了什么是函数。在这篇文章中，我们来谈谈那些看起来像函数，但实际上不是的东西。

## 按定义

不是所有的方程都是函数。`y = x + 1`是函数，而`y² + x² = 1`不是，因为“函数是多对一(或者有时是一对一)的关系”(这种情况下`y`有 1 或 2 个值对应一个`x`)。

并非所有的图形(笛卡尔坐标中的点集)都是函数。此图代表一个函数:

[![](img/7d7c676e347b8bfa81f6ecae75ca3366.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IfpNj1u7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://stereobooster.com/posts/not-a-function/line.svg)

但是这个不是:

[![](img/44b2a65da76e7dffd6d4df435a10fdfd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HF4XUzNl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://stereobooster.com/posts/not-a-function/circle.svg)

并非所有的表(元组集`(x, y)`)都是函数。这一个代表了一个功能:

| x | y |
| --- | --- |
| one | one |
| Two | Two |
| ... | ... |

但是这个不是:

| x | y |
| --- | --- |
| Two | one |
| Two | Two |
| ... | ... |

所有的功能都是关系，但不是所有的关系都是功能。

[![](img/ede2eb4755ffcdcc0dc63875ea56d3eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ljQuCQoS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://stereobooster.com/posts/not-a-function/relations-vs-functions.svg)

如果我们画一张集合`A`和`B`之间所有可能关系的表格，其中只有两个是函数(用`f`标记)

|  | 对应于集合 B 中的 0-1 元素 | 0 米 | one | 1 米 |
| --- | --- | --- | --- | --- |
| 集合 A 中的 0-1 元素 |  |  |  |  |
| 0 米 |  |  |  |  |
| one |  |  | f | * |
| 1 米 |  |  | f | * |

*多值函数(或多值函数)是将域中的单点映射到范围中可能的多个点的关系(在复变函数理论中)。

## 更多关于域

> 从`A`到`B`的函数是一个对象`f`，使得每个`a in A`都与一个对象`f(a) in B`唯一关联。定义一个函数的值的集合`A`被称为它的`domain`。
> 
> - [Mathworld Wolfram](http://mathworld.wolfram.com/Function.html)

这里有一个可能令人困惑的位函数，它要求输入集(域)的每个元素都对应于输出集(域)的某个元素。

`y = 1/x`呢，0 没有输出([至少没有一个版本都同意](https://www.hillelwayne.com/post/divide-by-zero/))。这里解释如下:0 不是给定函数定义域的一部分，或者你可以说函数`1/x`不是为 0 定义的。

后果:如果`y₁=x²/x`和`y₂=x`比`y₁≠y₂`大，因为`y₁`是为除 0 以外的所有实数定义的，而`y₂`是为所有实数定义的(`ℝ`)。

### 总功能

在编程中，它们有相关的术语:

> 总函数—即一个函数
> 
> *   这是为所有可能的输入定义的
> *   并且保证会终止。
> 
> -[Idris 教程](http://docs.idris-lang.org/en/latest/tutorial/theorems.html#totality-checking)

🤔意味着函数的**域**与“所有可能输入”的**类型**不是一回事。或者在编程中，我们需要一个稍微不同的函数定义。

🤔这里有两个条件:( 1)为所有输入定义,( 2)功能终止。在我看来，第二个条件在这里是多余的，因为如果函数永远不会终止，我们就永远不会得到答案，因此运算的结果是不确定的。例如，当你试图在机械计算器中除以 0 时，就会发生这种情况。

[![](img/dc7c1e603995d491d75913b317d7eed0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oTS942Ks--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://stereobooster.com/posts/not-a-function/division-by-zero.gif)

图片来源:[popularmechanics.com](https://www.popularmechanics.com/technology/gadgets/a20152/dividing-by-zero-will-mechanical-calculator/)。

## 非功能编程

### 无输入

我们是否应该将没有输入的“函数”视为函数？

🤔称之为函数合适吗？也许更好的名字是协程或过程？

如果它们产生一个以上的输出，则一个输出比否:

```
Math.random(); // 0.8240352303263008
Math.random(); // 0.1830674266691794
Date.now(); // 1562502871898
Date.now(); // 1562502872905 
```

🤔如果它们产生一个输出，例如，一个返回 singleton 的函数，该怎么办？可能不会(为了更详细地解释，我们需要谈论效果，这是即将到来的帖子的主题)。

### 多个输出为同一输入

不是函数:

```
let counter = 0;
const inc = x => (counter = counter + x);
inc(1); // 1
inc(1); // 2 
```

🤔有趣的是，我们将一个输出视为“一段时间内的一个输出”(例如后续调用)。一次输出多个怎么办？

```
const y = x => {
  if (x > 1 || x < -1)
    throw new Error("Function undefined for x > 1 or x < -1");
  const result = Math.sqrt(1 - x * x);
  return [-result, result];
}; 
```

首先，我们需要定义相同的输出意味着什么——我们如何在编程中为值定义相等(或相同)。

### 比较

编程中两个值相等的时候？我们可以考虑两种选择:

*   标称比较(身份)-对象只有在完全相同时才相等，例如，它们有一些唯一的标称标识符，这在计算机的情况下可以是内存引用(或指针)。
*   结构比较(相等)——如果对象的所有“成员”都相等，那么对象就是相等的，在最简单的情况下，我们可以一位一位地比较内存。

*边注*:对于原语值，像整数，有哪些值小到直接放入栈而不是堆名义比较和结构比较是一回事。

对于给定的示例:

```
y(0.5) === y(0.5); // false 
```

`y`不会产生名义上“相同”的结果。

```
y(0.5)[0] === y(0.5)[0]; // true
y(0.5)[1] === y(0.5)[1]; // true 
```

但是它产生了结构上“相同”的结果。我们可以选择任何类型的比较，并依赖于这个`y`将是或者将是**不是**是一个(数学)函数。

同样，我们可以让`y`返回名义上相同的结果:

```
const memoize = f => {
  const cache = new Map();
  return x => {
    if (!cache.has(x)) {
      cache.set(x, f(x));
    }
    return cache.get(x);
  };
};
const y1 = memoize(y); 
```

如你所见，`y1`对相同的输入
返回名义上相同的结果

```
y1(0.5) === y1(0.5); // true 
```

这里的权衡是我们需要更多的内存来存储输出。最有可能的是，它会提前为`new Map()`分配更大的内存，所以即使我们不调用`y1`，我们也需要付出代价(内存)。

另一方面，结构比较需要更多的 CPU 周期——在最坏的情况下我们需要一点一点地比较内存。

*附注*:在垃圾收集语言中，我们可以使用更少的内存进行名义比较，因为我们可以跟踪输出对象是否在使用中，如果它不在使用中，我们可以将其从缓存中移除(类似于 [`WeakMap`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap) 的工作方式，除了值而不是键)。

这个问题没有普适的答案，对于递归(循环图)数据结构，结构比较会失败，例如:

```
const a = {};
a.a = a; 
```

如果我们想要比较两个不同函数的值，名义比较将不起作用

```
JSON.parse("[-0.8660254037844386,0.8660254037844386]") === y1(0.5); // false 
```

🤔如何比较函数(如果我们把函数当值来讲)？如果我们想在结构上比较它们，我们应该比较它们产生的字节码吗？如果两个函数的字节码是由不同的编译器生成的会怎么样？如果是同一个函数，但是实现不一样怎么办，比如:

```
const fact1 = n => {
  let res = 1;
  for (let i = 1; i <= n; i++) {
    res = res * i;
  }
  return res;
};
const fact2 = n => (n < 2 ? 1 : n * fact(n - 1)); 
```

🤔如何实现反序列化对象的名义比较？我们应该存储所有反序列化的字符串以总是返回相同的引用吗？

实际上，编程语言(机器)可以结合使用这两种方法，例如:

*   首先比较引用，如果第一次检查是错误的，则退回到结构比较
*   或者在结构上比较原语(整数、字符串等。)并名义上比较其他变量(数组、对象等)。)
*   等等。

所以由开发人员来决定使用哪种比较。

### 列表和结构是有效的函数结果吗？

上面声明的函数`y`表示与`y² + x² = 1`相同的`x`和`y`之间的关系。但是早些时候我们得出结论`y² + x² = 1`是方程而不是函数。🤔这是否意味着`y`不是函数？

嗯，我会说它仍然是一个函数(`y`有一个单一的输出列表)。这是一个例子，说明数学函数的概念(柏拉图的概念)并不总是直接转化为计算(计算与物理学的关系更密切)。

[![](img/9d96daf7eca8040a76dfabced308dfee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0OiDatmq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://stereobooster.com/posts/not-a-function/purity.png)

(https://xkcd.com/435/的修改版

在数学中，他们不谈论计算复杂性(“大 O 符号”)，只要相同的输入产生相同的输出，数学家就会认为这些是相同的函数，例如，冒泡排序和合并排序。从计算机科学的角度来看，它们有不同的时间和空间复杂度。

函数的(柏拉图式)思想在数学和计算机科学中非常有用，它可能需要一些调整或重新解释。
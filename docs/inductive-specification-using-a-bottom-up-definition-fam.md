# 使用自下而上定义的归纳规范

> 原文：<https://dev.to/dwayne/inductive-specification-using-a-bottom-up-definition-fam>

**Psst** : *我在本文末尾分享的论文[归纳图和函数图算法](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.28.9377&rank=1)解释了为什么归纳规范对函数式编程如此有用。*

另一种编写归纳规范的方式是使用**自底向上的定义**。

例如，从[开始的集合`S`这里的](https://dev.to/dwayne/inductive-specification-using-a-top-down-definition-63p)可以使用自底向上的归纳规范定义如下:

`S`是满足以下性质的`ℕ`中包含的最小集合:

1.  `0 ∊ S`，以及
2.  如果`n ∊ S`那么`n + 5 ∊ S`。

我们不得不说“`S`是最小的”，以保证`S`是唯一定义的。

我们可以使用这个定义来生成`S`中的自然数。根据定义，`0 ∊ S`和条件 2 得出`0 + 5 = 5 ∊ S`。通过重复应用条件 2，我们可以看到所有 5 的倍数都在`S`中。

## 何苦呢？

原因和之前的[一样。](https://dev.to/dwayne/inductive-specification-using-a-top-down-definition-63p)

此外，自底向上的定义尤其可用于生成集合的值。

作为另一个例子，这里是所有自然数列表的集合`L`的自底向上归纳规范:

1.  `[] ∊ L`，
2.  如果`n ∊ ℕ`和`l ∊ L`那么`n :: l ∊ L`，
3.  `L`里没别的了。

**N.B.** *条件 3 是`L`是这样的集合中最小的一个的另一种说法。*

最棒的是，从自底向上的定义中，我们可以使用代数数据类型来表示集合的值。

```
type List a
  = Empty           -- condition 1
  | Cons a (List a) -- condition 2
  -- condition 3 is implicit 
```

Enter fullscreen mode Exit fullscreen mode

然后，列表上的许多函数可以通过递归函数定义简洁地给出。

在[归纳图和功能图算法](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.28.9377&rank=1)中，Martin Erwig 将图视为归纳定义的数据类型，然后继续展示如何高效地实现图，以及如何通过递归函数定义简洁地给出图算法。

## 挑战

为集合`S = {2n + 3m + 1 | n, m ∊ ℕ}`写一个自底向上的归纳规范。

**N.B.** *这篇文章基于我对第一章的[人民笔记。](https://github.com/dwayne/eopl3/blob/master/03-ch1.md)*
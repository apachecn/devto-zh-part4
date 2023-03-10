# 重构 z 索引

> 原文：<https://dev.to/p4lm/refactoring-z-index-389j>

曾经在 CSS 中找到一个`999`的`z-index`并想知道它下面是否真的有 998 个其他元素？

> *有一次我甚至偶然发现了一个 z 索引 2147483647*

有一次我甚至偶然发现了一个 z 索引 2147483647。我觉得那个数字看着眼熟，*“这不是 Int32 max 值吗？🤔"*是的，后来谷歌搜索证实了我的怀疑:你可以使用的最高 z 索引是 32 位整数的最大值。

但是，即使你*可以*使用非常高的数字作为 z-index，为什么*应该*你呢？答案很简单:不应该！高数字是不必要的，只会让你更难看出什么更高和更低，99999 和 148851 之间的区别可能很难一眼看出。那么，我们怎样才能做得更好呢？

## 我的重构

我在代码库中做的第一件事是收集一个常量文件中的所有索引。我们使用了[风格的组件](https://www.styled-components.com/)，所以我的例子是用 javascript 写的，但是当然同样的事情也可以用 [Sass](https://sass-lang.com/) 来完成。

```
export const zIndexes = {
  AlertBox: 10,
  ModalOpacity: 100,
  PresentationContainer: 745,
  HamburgerMenu: 999,
  MenuOpacity: 999,
  Arrows: 9999,
  MenuWrapper: 12500,
  FullScreenButton: 99999,
  ProgressBar: 999999
}; 
```

请注意，我是按升序排列的。通过将所有的 z 索引放在同一个文件中，我们可以很好地了解它们。我从中复制值的地方现在引用了一个常量，而不是实际值。我认为从可读性的角度来看这也是好的——当你查看一个组件/类的样式时，你不必关心它的值是什么。

下一步是把这些数字简化。如果我们有一个新元素需要现有元素之间的一个值，我们可以重新分配它们，没什么大不了的。

```
export const zIndexes = {
  AlertBox: 1,
  ModalOpacity: 2,
  PresentationContainer: 3,
  HamburgerMenu: 4,
  MenuOpacity: 4,
  Arrows: 5,
  MenuWrapper: 6,
  FullScreenButton: 7,
  ProgressBar: 8
}; 
```
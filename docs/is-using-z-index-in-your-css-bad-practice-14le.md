# 在你的 CSS 中使用 z-index 是不好的做法吗？

> 原文：<https://dev.to/darrenvong/is-using-z-index-in-your-css-bad-practice-14le>

你最后一次看到像这样使用`z-index` CSS 属性是什么时候:

```
.some-element {
  z-index: 99999999;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，假设您希望另一个元素显示在带有“some-element”类的元素之上，这是一个快速而肮脏的(但似乎很常见！)解决这个问题的方法是在上面的 z-index 值的末尾添加另一个“9 ”,并将其应用于这个其他元素。对于较大的 web 应用程序，这可能会很快变得难以管理。

由于默认的排序是由 HTML 标记的顺序决定的，离底部最近的元素出现在顶部，我们不应该用它来代替 z-index 吗？你能想出一个仅仅依靠标记顺序是不够的用例吗？
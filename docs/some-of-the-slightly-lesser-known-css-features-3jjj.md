# 一些鲜为人知的 CSS 特性

> 原文：<https://dev.to/micahsgilbert/some-of-the-slightly-lesser-known-css-features-3jjj>

这里列出的功能来自[CSS 2019](https://2019.stateofcss.com/features/)的状态。

## 滚动捕捉

Scroll snap 顾名思义。它让用户滚动“捕捉”到页面上的某些点。
[https://codepen.io/micahsgilbert/embed/Yopxze?height=600&default-tab=result&embed-version=2](https://codepen.io/micahsgilbert/embed/Yopxze?height=600&default-tab=result&embed-version=2)
更多信息[这里](https://css-tricks.com/practical-css-scroll-snapping/)。

## 过卷行为

通常，当带有滚动条的元素一直向下或向上滚动时，父元素也会开始滚动。`overscroll-behavior`可以通过不允许父元素滚动来防止这种情况。
[https://codepen.io/micahsgilbert/embed/qzqXoo?height=600&default-tab=result&embed-version=2](https://codepen.io/micahsgilbert/embed/qzqXoo?height=600&default-tab=result&embed-version=2)
(这里的演示有点 finnicky。你必须在一个没有文本的区域滚动，当你到达底部时停止滚动，然后重新开始。)

更多信息[此处](https://developer.mozilla.org/en-US/docs/Web/CSS/overscroll-behavior)

## 意志——变化

`will-change`很奇怪。这是一种告诉浏览器一个元素的属性将会改变的方式。 [Mozilla 文档](https://developer.mozilla.org/en-US/docs/Web/CSS/will-change)声明它应该只在发现性能问题时使用，而不是在预期性能问题时使用。
[https://codepen.io/micahsgilbert/embed/EBNwxL?height=600&default-tab=result&embed-version=2](https://codepen.io/micahsgilbert/embed/EBNwxL?height=600&default-tab=result&embed-version=2)
这可能是一个无用的演示，但它表明有时快速的变化可能需要一个 will-change 标记。

## @支持

`@supports`是检查浏览器是否支持某个特性的查询。

```
@supports (display: grid) {
  /* code here that gives the grid display instructions */
}

@supports not (overscroll-behavior) {
  /* If it doesn't support overscroll-behavior */
} 
```

更多信息[此处](https://developer.mozilla.org/en-US/docs/Web/CSS/@supports)

## 书写模式

这只是用来改变元素中文本的方向。这是旋转标签的另一种选择。
[https://codepen.io/micahsgilbert/embed/OebxBp?height=600&default-tab=result&embed-version=2](https://codepen.io/micahsgilbert/embed/OebxBp?height=600&default-tab=result&embed-version=2)
更多信息[此处](https://developer.mozilla.org/en-US/docs/Web/CSS/writing-mode)

## 包装完毕

我希望你已经从一些新的 CSS 特性中学到了一些新的东西。如果有我错过的或者你想补充的，请在评论里写出来！
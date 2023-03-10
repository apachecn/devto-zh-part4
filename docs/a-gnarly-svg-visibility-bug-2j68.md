# 一个粗糙的 SVG 可见性错误

> 原文：<https://dev.to/bryce/a-gnarly-svg-visibility-bug-2j68>

或者:通过记住它们的 HTML 根来更深入地理解 SVG 是如何呈现的。

我喜欢 SVG。与图像相比，它们很小&在渲染时允许更大的灵活性。但是这种灵活性也为奇怪的问题提供了更多的空间。以下是阻碍我工作太久的一项任务的原因:

[https://codesandbox.io/embed/chrome-svg-bug-hppef](https://codesandbox.io/embed/chrome-svg-bug-hppef)

当一个 SVG 在页面上可见的组件和另一个不可见的组件之间共享时，隐藏的组件在 DOM 树中的位置比 T2 高，可见的组件将不能正确呈现。所以当对多个组件使用 CSS `display`规则时，DOM 中的排序是最重要的。

[![Interesting](img/4f2d816fefdec00218541d9c468aef69.png)](https://i.giphy.com/media/c4Nc0v0g15g9G/giphy.gif)

边注:有趣的是，一些颜色会渗出，因为你可能会注意到小小的绿色边框，但忽略它，因为它只是使调试花费更长的时间。

## 我是怎么抓到它的

[![Invisible svg](img/331669e35a18b80e3c696333bbdcd442.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fw7iQXJL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kbgdckznc2ynu8pw7439.png)

通过`document.getElementsByTagName('svg')`获得页面上的所有 SVG，我能够看到将鼠标悬停在它们上面看不到的实例。从那以后，我试着剔除包装它们的不可见组件来返回`null`，问题得到了解决。✨

## 预防

我报告这是一个 Chromium 错误，但结果这与 2013 年[提交的错误报告是一样的。(令人惊讶的是，Safari 没有这个问题。)😴因此，这里有一些变通办法，而不是推动解决方案:](https://bugs.chromium.org/p/chromium/issues/detail?id=258029)

#### 特指代码

不要使用 CSS 和`@media`查询来隐藏页面上的某些元素，而是使用视口大小检测来防止这些组件被渲染。这个 bug 只在第一个 SVG 被隐藏时存在，所以如果它们都可见，它就会神奇地工作！

#### 多泛指

1.  在渲染 svg 时，结合使用 [svg-inject](https://github.com/iconfu/svg-inject) 和`makeIdsUnique`参数。

2.  如果使用 React 或其他虚拟 DOM 库，使用类似于`react-svg`的东西创建一个包装器组件，其中`beforeInjection`可以调用上面的`SVGInject`函数来修改`id`并使它们唯一。

* * *

[![Deflate](img/65c5f4f8706ec10a845aaeb07d6b427e.png)](https://i.giphy.com/media/NM8ZZLPsPk1nq/giphy.gif)

(最后)就这样了。我很高兴我终于搞清楚了。希望这能防止其他人掉进 SVG 兔子洞。
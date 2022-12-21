# 解开相互依赖的反应上下文？

> 原文：<https://dev.to/wolverineks/untangle-react-contexts-that-depend-on-each-other-57hh>

最近在 Twitter 上有一个[问题](https://twitter.com/ovidme/status/1162873760489381894)被问到，大概是如何解开两个使用彼此提供的状态/回调的 react 上下文。

有人提出了几种答案，从将它们组合成一个上下文，到使用一个上下文的回调来同步另一个上下文的状态。

虽然我毫不怀疑所提供的任何解决方案都可以发挥作用，但我忍不住想到了另一个我认为更符合 react 风格的解决方案 [composition](https://dev.to/bouhm/thinking-in-react-component-composition-fp5) 。

所以，事不宜迟，我的解决方案是:

[https://codesandbox.io/embed/fast-sun-w0tsj](https://codesandbox.io/embed/fast-sun-w0tsj)

就像“做一件事，并把它做好”的 unix 哲学一样，每个上下文都被简化为一个单独的责任。这两个原始上下文现在可以独立使用，第三个上下文只负责合成。

请随时提供反馈。好吗？不好？赞成/反对的？
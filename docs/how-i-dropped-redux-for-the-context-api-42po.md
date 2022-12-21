# 我如何为上下文 API 放弃 Redux

> 原文：<https://dev.to/brunosabot/how-i-dropped-redux-for-the-context-api-42po>

React 16 引入了一个新的上下文 API 来取代被弃用的那个。好了，距离 16.3 版本发布已经一年多了，但在 React 生态系统中似乎还很新鲜。

这个新的 API 有望解决以前使用上下文的实验性方法的许多问题。对我来说，它做得更多；它改变了我制作 React 应用程序的方式。这是我如何管理它的故事。

我不会给出 Redux 如何工作的课程。如果你想复习，你可以查看丹·阿布拉莫夫在 Egghead 上的精彩课程。另外，你最终会把 Redux 从你的应用中移除，所以我们需要一个完整的课程吗？

理解代码有几个要求:我会用 [React 钩子](https://en.reactjs.org/docs/hooks-intro.html)和 [React 片段](https://en.reactjs.org/docs/fragments.html)的简称`<>`。

好吧，假设我们有一个应用程序可以告诉我是否有空去喝杯啤酒。它包括以下内容:
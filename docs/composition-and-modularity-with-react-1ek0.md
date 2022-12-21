# React 的组合和模块化

> 原文：<https://dev.to/nicomaligne/composition-and-modularity-with-react-1ek0>

本文基于复合模式。在 2016 年[凤凰城](https://www.youtube.com/watch?time_continue=1&v=hEGg-3pIHlE)反应堆 [Ryan Florence](https://ryanflorence.com/) 的一次演讲中，它首次亮相。
我是在 Kent.C.Dodds 的[课程](https://egghead.io/courses/advanced-react-component-patterns)中用 [Egghead.io](https://egghead.io/) 发现的。你可以在他的[博客](https://kentcdodds.com/)上找到更多资源。
该模式强调模块化和组件组合。

在本文中，我们将使用 [React 上下文](https://reactjs.org/docs/context.html)和 [React 钩子 api](https://en.reactjs.org/docs/hooks-reference.html) 。

TL；在我的公司，我在构建可重用的共享组件时遇到了一些问题。我将向您展示如何解决这些问题，增强组合，并且仍然给用户定制行为的可能性。

## 整块构件

您需要创建一个简单的共享组件，一个带有 select all elements 操作的 DataList。
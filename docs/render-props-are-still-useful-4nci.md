# 渲染道具还是有用的

> 原文：<https://dev.to/carlrippon/render-props-are-still-useful-4nci>

render props 模式是在组件之间共享逻辑的一种流行方式。因为 React 16.8 [定制钩子](https://reactjs.org/docs/hooks-intro.html)是在组件之间共享逻辑的一种更优雅的方式。那么，现在不需要渲染道具了？不要！渲染道具对于构建可重用组件仍然很有用...

## 什么是渲染道具？

渲染道具是一种渲染某物的功能，即返回 JSX 的功能:
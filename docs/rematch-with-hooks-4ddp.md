# 用钩子重赛

> 原文：<https://dev.to/kosslebedev/rematch-with-hooks-4ddp>

如果您一直在使用 Rematch 来管理应用程序中的状态，那么最新发布的添加了钩子支持的`react-redux`应该会让您非常兴奋。

Rematch 一直试图保持与现有`react-redux` API 的兼容性，对于新发布的支持钩子的版本也是如此！

> 除了有更多可读和可重用的代码，钩子允许我们在用 TypeScript 添加类型安全时做更少的工作。

我们来看一个“老办法”做事的例子。下面是一个组件，它将用户列表存储在 Redux store 中，并在安装组件时加载用户:
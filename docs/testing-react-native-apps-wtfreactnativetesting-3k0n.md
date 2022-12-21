# 测试 React 原生应用？WtfReactNativeTesting

> 原文：<https://dev.to/tuantvk/testing-react-native-apps-wtfreactnativetesting-3k0n>

## 我们的单元测试到底是什么？🚨

我们使用**“单元测试”**来指代独立于 React 原生框架的函数和普通 JavaScript 对象的测试。这意味着我们没有测试任何依赖 React Native 的组件。

例如，一个单元可以是类中的单个方法和函数，或者实际上是任何一小部分功能。我们在这些测试中模拟出依赖关系，以便我们可以孤立地测试单个方法和函数。

这些测试是使用测试框架编写的，对于本文，我将使用 [Jest](https://jestjs.io/) ，javascript 测试框架以及 [Enzyme](https://airbnb.io/enzyme/) 和 [React Native 测试库](https://github.com/callstack/react-native-testing-library)。

查看我的回购上的所有信息 [WtfReactNativeTesting](https://github.com/tuantvk/WtfReactNativeTesting)
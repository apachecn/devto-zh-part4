# 动画反应与帧运动

> 原文：<https://dev.to/kosslebedev/animate-react-with-framer-motion-fo2>

[Framer-motion](https://www.framer.com/motion/) 是一个为 [Framer](https://www.framer.com/) 中的动画提供动力的库，它现在是一个独立的包，我们可以在 React 应用程序中使用。它有一个非常简单的声明式 API，使得用最少的代码创建和编排复杂的动画变得容易。在这篇文章中，我们将从非常基础的动画开始，逐渐过渡到更高级的。

*注意:由于 GIF 图像的帧速率较低，文章中的动画示例可能看起来不流畅。放心，真正的动画都是**油滑**。你可以在这里的的[沙盒里和他们一起玩。](https://codesandbox.io/s/framer-motion-intro-96bu3)*

## 设置

我们可以用`yarn add framer-motion`命令安装 framer-motion。

为了动画元素，我们需要抛弃原始的 HTML 元素(`div`、`span`、`path`等)。)而支持他们的“注入运动”的对手- `motion.div`、`motion.span`、`motion.path`等。这些元素暴露了我们添加动画所需的属性。

## 让事情动起来

为了创建最简单的动画，我们可以指定`animate`属性，该属性接受一个具有我们想要制作动画的 CSS 属性的对象。这就是我们如何制作`div`的不透明度和背景颜色的动画:
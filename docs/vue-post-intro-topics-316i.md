# 4 个介绍 Vue 后要探索的主题

> 原文：<https://dev.to/terabytetiger/vue-post-intro-topics-316i>

## [T1】简介](#intro)

[![aspittel image](img/45a8d86fe0ff981bf7cdb45b471a3c5c.png)](/aspittel) [## Vue 初学者完全指南

### 阿里·斯皮特尔 3 月 20 日 19 时 8 分阅读

#beginners #javascript #vue #frontend](/aspittel/a-complete-beginners-guide-to-vue-422n)

当 Ali 在 Vue 上发布了她完整的初学者指南时，我决定这是一个尝试的好时机，因为它不断出现，而我在 React 上度过了特别沮丧的一天。从那时起，我就一直在 Vue 上投入时间，并且非常喜欢它！在这篇文章中，我想浏览一下(数字)和我发现在阿里指南之后有助于了解的主题(除了在那篇文章末尾列出的其他伟大文章)。

## 1。单个文件组件

关于 Vue，我非常喜欢的一点是[单个文件组件](https://vuejs.org/v2/guide/single-file-components.html)的结构——文件扩展名为`.vue`的文件。对我来说，HTML、CSS 和 JavaScript 之间的明显区别非常明显。

```
<template>
  <div>
    This is where the HTML goes.
  </div>
</template>

<script>
// This is where the JS goes.
module.exports = {
  name: 'ComponentName'
}
</script>

<style scoped>
/* This is where the CSS goes.*/
</style> 
```

Enter fullscreen mode Exit fullscreen mode

## 2。限定范围的样式

你可能已经注意到我没有使用`<style>`标签，而是使用了`<style scoped>`标签。不同之处在于，作用域样式只影响应用它们的组件和任何子组件！这有助于将 CSS 保持在一个地方，而不需要大量的`#id`目标或内联样式。

## 3。路由器视图

Vue 路由器是我刚开始的时候比较沮丧的东西之一，但主要是因为我低估了它的强大。

Router 允许你创建一个[单页应用](https://en.wikipedia.org/wiki/Single-page_application)，而不需要你正常的 Vue 工作流程之外的太多工作。您创建一个布局组件，它包括一个`<router-view>`组件和一个`router.js`文件，您可以在其中指定您希望提供哪些视图。

当我认为我需要创建两个布局页面并在它们之间导航，以便在某些情况下可以在页面中进一步嵌套 router 元素时，我未能完全掌握 Router 的功能。然而，Vue 路由器的创造者远远领先于我，你可以[将一个`<router-view>`嵌套在另一个`<router-view>`](https://router.vuejs.org/guide/essentials/nested-routes.html) 中！

## 4。武契特

当你开始构建更大更酷的应用程序时，你会发现跟踪所有组件的状态变得越来越难。Vuex 允许你创建一个“国家银行”,在那里你可以保存许多组件需要访问的所有重要细节。

请确保在申报商店时，将`new Vuex.Store(`包括在这一行:

```
const store = new Vuex.Store({ 
```

Enter fullscreen mode Exit fullscreen mode

根据我的经验，我不知道

液体错误:内部

## 快速提示

我不知道还能放在哪里:

### 从我的奋斗中学习

当我第一次对某件事感兴趣时，我通常会全身心地投入其中。所以在早上准备的时候，我开始玩一些 Vue 会议对话。其中一个是尤雨溪(Vue 的创造者):

[https://www.youtube.com/embed/8Hgt9HYaCDA](https://www.youtube.com/embed/8Hgt9HYaCDA)

我非常兴奋 Vue 的新版本即将推出，所有的好处听起来都很棒！

所以当我那天去上班时，我做的第一件事就是运行下面的命令，它返回了 **3。#.#** -我已经有第三版了！*甜！*

```
$vue --version 
```

Enter fullscreen mode Exit fullscreen mode

但遗憾的是，这是返回的 CLI 版本，而不是正在使用的 Vue 版本(可以通过 Vue 开发工具找到)。

### 正式文件

有单独的停靠站点，用于

*   vista . js
*   [Vuex](https://vuex.vuejs.org/)
*   [VueCLI](https://cli.vuejs.org/)
*   vista 路由器

它们中的大部分彼此之间都有轻微的接触，但是最好记住你可能在你正在寻找的 doc-lite 版本上。

### 教程‘跳过’

在许多 Vue 指南中，我注意到他们是这样开始的:

```
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

这很好...除了我正在处理一个`.vue`文件，这是一个`.js`文件。

通常，如果您跳到下一个代码块，它要么是单个文件组件格式，要么开始检查如何转换到单个文件组件格式。

## 关闭

希望我辛苦学到的一些东西可以作为指针，帮助你在 Vue 中起步！我每天都在学习很多关于 Vue 和做事情的最佳方法，但是如果你有问题，我很乐意帮助你或者帮助你找到更有知识的人！

凯西·霍纳在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的太空照片
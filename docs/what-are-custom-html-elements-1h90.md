# 什么是自定义 HTML 元素？

> 原文：<https://dev.to/firstclown/what-are-custom-html-elements-1h90>

当我在研究如何使用现代 JavaScript 框架在我的应用程序中构建我的 JavaScript 时，我不断遇到同样的问题。我想使用基于组件的设计，但我并不总是希望构建一个单页面应用程序。有时候，我只想在页面上添加一个与框架完全无关的组件。

在 Angular 这样的框架中，这几乎是不可能的。我一直认为 Angular 是一个要么全有要么全无的框架。要么都有棱角，要么都没有。如果我有一个遗留应用程序，我想一点一点地升级，全部或什么都没有不是我要找的。

Vue 给自己贴上了现代 JavaScript 组件的“可增量采用”框架的标签。我发现这大体上是对的，但是我一直在纠结如何最好地逐步采用它。我是不是应该开始创建 Vue 实例并把我所有的 JavaScript 放在那里？我是否构建组件，然后试图找出如何从我当前的大型应用程序开始？当我的页面只使用一小部分组件时，我如何不加载每个页面的所有组件？这是否意味着我必须放弃我的全局 JavaScript 文件的想法，现在为每个页面创建一个 JavaScript 文件？这不正是我想要避免的吗？！？

在使用 [Vue CLI](https://cli.vuejs.org/) 时，我注意到有一个选项可以将 Vue 组件构建为本地 web 组件。为了弄清楚这是什么意思，我钻研了文档。

## Web 组件标准

Web 组件标准是一个被主流浏览器采用的标准，作为开发人员创建他们自己的 HTML 元素的一种方式的 T2。

这一开始听起来可能很奇怪，也不是很有用。为什么我们要创建一个新的`<div>`或`<p>`标签？但是有一些 HTML 元素背后有很多有用的功能，比如`<video>`、`<canvas>`，甚至还有`<select>`元素。

`<select>`实际上具有许多很酷的功能。默认情况下，您可以定义一个选择框和一些`<option>`,浏览器知道将它们作为下拉选择显示给用户，并限制用户只能选择一个。如果您传递属性`multiple`，功能将发生变化，允许用户选择多个选项，并将控件显示为滚动框而不是下拉列表。

这显示了简单组件可以有多复杂。它们有结构(一个`select`包含`option` s)、表示(显示为下拉列表或滚动框)和逻辑(用户输入被验证是否可以选择多个选项)。这个功能听起来很像 JavaScript 组件！

## 构造一个简单的自定义元素

如果你知道如何在 Vue 中构建一个组件，你已经知道如何构建一个定制的 HTML 元素，只需要创建一个新的独立的 Vue 组件！

由于 Vue 组件就像 HTML 元素一样使用，所以如果您在 Vue 中构建一个标准的单个文件组件，您可以从项目中将它导出为一个新的 HTML 元素。

我希望在另一篇文章中介绍这个过程，但是我们现在可以看一个简单的例子。

我记得 1999 年网络发展的奇迹年代。那时一切皆有可能，包括美妙的`<blink>`标签。可悲的是，每个浏览器前段时间都杀死了这个可怜的标签，但我们可以让它重新流行起来！

下面，我定义了一个新的组件，它将获取标签之间的任何内容，并使其闪烁。就像过去的美好时光一样！

```
<template>
  <span ref="blinkyText"><slot></slot></span>
</template>

<script>
export default {
  name: "blink",
  mounted() {
    setInterval(() => {
      this.$refs.blinkyText.classList.toggle("onoff");
    }, 500);
  }
};
</script>

<style>
.onoff {
  visibility: hidden;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

然后，我可以使用 VueCLI 将它编译成一个[自定义 HTML 元素，并将其部署到任何一个旧网页上，如下所示:](https://cli.vuejs.org/guide/build-targets.html#bundle-that-registers-multiple-web-components) 

```
<script src="https://cdn.jsdelivr.net/npm/vue@2.6.8/dist/vue.js"></script> <script src="/js/my-blink.min.js" defer></script> 
<my-blink>Party Like It's 1999!!!</my-blink> 
```

Enter fullscreen mode Exit fullscreen mode

该标签与任何框架或其他 JavaScript 无关。你不需要一个`main.js`或者其他任何东西来安装这个标签或者启动它。您只需包含 Vue JavaScript 文件，您的标签 JavaScript 文件，然后像使用任何其他 HTML 标签一样使用标签！

## 以后的文章会覆盖更多

最后，我将围绕如何构建这种风格的 Vue 组件以及如何将其绑定到 Vuex 中以允许这些组件相互通信撰写两篇文章。以下是一些已经完成的工作:

*   [设置 Vue CLI 项目以构建自定义 HTML 元素](https://jerickson.net/setting-vue-cli-project-custom-html-elements)
*   [从 Vue CLI 构建定制元素](https://jerickson.net/building-custom-elements-vue-cli)
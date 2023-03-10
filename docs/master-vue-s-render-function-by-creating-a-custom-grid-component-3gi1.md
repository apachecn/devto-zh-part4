# 通过创建自定义网格组件掌握 Vue 的渲染功能

> 原文：<https://dev.to/chgldev/master-vue-s-render-function-by-creating-a-custom-grid-component-3gi1>

对于那些用 ReactJs 编写前端应用程序的人来说，你可能很熟悉`render`函数来控制组件准备好之后呈现的内容。

在 Vue 中，你通常使用它的[模板解决方案](https://vuejs.org/v2/guide/syntax.html)来控制渲染的内容。它真的很强大，并且提供了很多以`v-`为前缀的[自定义指令](https://vuejs.org/v2/guide/syntax.html)。例如，您可以根据当前状态有条件地呈现一个 DOM 节点。

虽然这已经提供了很大的灵活性，但有时您可能会使用 [Vue 的渲染功能](https://vuejs.org/v2/guide/render-function.html)来完全控制正在渲染的 html。想象一下，必须将自定义类属性应用于传递给自定义组件的子组件。

这正是我们在本教程中要做的。

我将利用 [Tailwind CSS](https://tailwindcss.com/docs/what-is-tailwind/) ，因为它提供了基本 flexbox 网格所需的一切。我发现它使用的类名非常直观，而且它的实用优先方法与 Vue 组件非常匹配。

## 渲染功能

使用 render 可以让你控制一个叫做`createElement`，或者简称为`h`的函数。

**提示**:在本教程中，我将命名`createElement`函数`h`。

一个非常简单的渲染函数示例如下:
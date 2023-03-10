# 如何在 Vue CLI 3 项目中使用 LitElement

> 原文：<https://dev.to/alangdm/how-to-use-litelement-in-a-vue-cli-3-project-164a>

我是 web 组件的忠实粉丝，我认为它们因为许多不同的原因而令人敬畏，它们基本上是最终让我喜欢上 web 前端开发的原因。

然而，我不是在这里开始另一篇关于 web 组件如何好或不好，它们将如何取代或不取代‘X’框架，或类似的东西，许多比我更了解这些主题的人已经深入讨论过了。

我知道框架有多棒，也知道有多少人已经在生产中成功地使用了它们，但在我看来，web 组件最酷的地方在于它们[可以在任何框架](https://custom-elements-everywhere.com/)中使用，或者根本不用框架，我在这里向您展示框架(在这里是 Vue)与 web 组件(在这里是基于 LitElement)共存的几种方式。

## 使用从 npm 安装的 web 组件

从 npm 安装的 Web 组件在 Vue 中其实不需要任何额外的配置只要你只需要支持 evergreen 浏览器就可以了，很好看不是吗？

现在，我知道这个世界并不都是美好的，并不是每个人都使用最新的浏览器，所以关于如何配置 polyfills 的信息，我建议你阅读来自 Vaadin 的[这篇文章(顺便说一下，他们有一个很棒的 web 组件集合)。](https://vaadin.com/tutorials/using-web-components-in-vue)

Vue CLI 3 应用程序中组件的实际用法如下:

```
<template>
  ...
  <awesome-component 
    :attribute="attribute" 
    :property-only.prop="property"
    @an-event="callback"
    ref="awesomeReference"
  >
  </awesome-component>
  ...
</template>
<script>
import 'awesome-component';

export default {...};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## 在 Vue CLI 3 应用程序中基于 LitElement 创建组件

老实说，我真的不知道你为什么真的想这么做，但最酷的是你可以这么做，你所需要做的就是在安装 LitElement 后，将以下内容添加到你的`vue.config.js`的`transpileDependencies`属性中:

```
module.exports = {
  transpileDependencies: [/node_modules(?:\/|\\)lit-element|lit-html/],
}; 
```

Enter fullscreen mode Exit fullscreen mode

这是为了显式转换 LitElement 和 lit-html，因为它们只提供 es 模块导出(有关更多信息，请查看 [Vue CLI 文档](https://cli.vuejs.org/config/#transpiledependencies))。

就这样，你可以在你的 vue 应用中随心所欲地创建 LitElement 组件(记住，对于 polyfills 配置检查 [Vaadin 的 post out](https://vaadin.com/tutorials/using-web-components-in-vue) )。

关于这一点，如果您正在为 web 组件使用任何其他基类，这个配置也将是有用的，只需为您选择的类更改`lit-element`。

## 结论

有时候，作为开发人员，我们可能太专注于证明我们选择的技术是至高无上的，以至于我们可能忘记了它们可以彼此共存，这只是提醒我们没有必要争论 web 组件是否可以替代框架，它们是每个人都可以受益的标准 web api，我希望这篇文章能够激励更多的 web 开发人员尝试它们。

感谢你的阅读，这是我在 dev 的第一篇文章，所以请友好点😄
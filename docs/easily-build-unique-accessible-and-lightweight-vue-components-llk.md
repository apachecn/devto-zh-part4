# 轻松构建独特、易于使用的轻量级 Vue 组件

> 原文：<https://dev.to/stegosource/easily-build-unique-accessible-and-lightweight-vue-components-llk>

**TL；dr:**check out[Vuetensils](https://vuetensils.stegosource.com/)如果你关心构建语义和可访问的 Vue 项目，但是更喜欢使用你自己的定制风格。

在过去的几年里，我用 Vue.js 构建了几个网站和应用程序。其中一些是基于第三方库构建的，如 [Vuetify](https://vuetifyjs.com/en/) 或 [Bootstrap-vue](https://bootstrap-vue.js.org/) ，其他的则完全是定制的。

这两种方法都有很大的好处，但是我为我所做的工作感到自豪，许多项目都有这样一件事，你离开时会想，“我本可以做得更好。”

一般来说，当我使用第三方库时，我会对以下一个或多个问题感到内疚:

*   这增加了我的包的大小，因为我必须导入比我实际使用的更多的代码。
*   该项目最终看起来与使用相同库的任何其他项目一样。
*   由于特殊性问题，我不得不通过用一些粗糙的 CSS 覆盖样式来定制项目，因此更多的 CSS 没有被使用。
*   第三方库实际上在编写语义或可访问组件方面做得并不好。

另一方面，当我自己设计/编码所有东西时，我的抱怨是:

*   如果我用图书馆的话，要花一半的时间。
*   每当出现我们还没有考虑到的新特性需求时，我也必须手工构建。
*   获得正确的语义和可访问性是困难的，并且为每个项目从头开始做是大量的工作。
*   我不知道我为一个模态实现了多少次相同的代码

最近，我开始着手更多的项目，从零开始构建它们。对于每个项目，我都有一些更有用的代码，我可以复制到下一个项目，只需替换样式。这让我想到:

> 如果有一个 Vue 库来处理所有的语义和可访问性，但是让我使用自己的定制样式，那会怎么样？

## 为什么可访问性很重要

已经有太多关于可访问性为什么重要的博客文章了，所以我不会说太多细节，但是这里是我关心的要点。

*   **避免诉讼**:2019 年，ADA 相关诉讼平均每天 8 起([来源](https://blog.usablenet.com/midyear-ada-web-accessibility-lawsuit-report-blog))。
*   **更好的业务**:实际上，有某种障碍的用户数量惊人。如果他们不能使用你的网站，你就会失去点击、电子邮件、销售、转化等。
*   更好的代码:自从关注可访问性之后，我发现我的代码有了很大的改进。语义对可访问性起着很大的作用，因此，我最终获得了更好的 SEO。
*   感觉棒极了:更不用说，当我在写更具包容性的代码时，我感觉自己变成了一个更好的人。放弃可访问性感觉像是一种伤害。

所有这些都很好，但是有一个问题:**可访问性并不容易**。

你必须自学许多咏叹调角色，解释键盘导航，确保焦点状态有意义，等等。事实上，对于非常有才华的开发人员来说，尝试实现可访问性也很容易，只是比他们什么都不做更糟糕。

有什么解决办法？找一个已经有这些东西的图书馆(见下文)。

## 介绍[vuetensil](https://vuetensils.stegosource.com/)

Vuetensils 是一个非样式化的 Vue 组件的集合，侧重于可访问性、语义和完全定制的设计。

它包括满足我的 UI 需求的组件，比如警告、模态、下拉菜单、输入等等，还有一些功能组件，比如一个图像[惰性加载器](https://en.wikipedia.org/wiki/Lazy_loading)，一个等待承诺的组件，以及一个[交叉点观察器](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)的包装器。

这些组件尽可能遵循来自 [WAI-ARIA](https://www.w3.org/WAI/standards-guidelines/aria/) 和 [WCAG](https://www.w3.org/TR/WCAG20/) 标准的最佳实践，并提供对类似[键盘导航](https://webaim.org/techniques/keyboard/)、[焦点捕捉](https://medium.com/@im_rahul/focus-trapping-looping-b3ee658e5177)，以及管理 [aria 角色和属性](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques)等特性的支持。

#### 什么是“裸”组件？

Vuetensils 组件几乎完全没有造型。这是故意的。它们被设计成提供功能需求的起点，同时期望你编写 CSS(即把他们打扮起来)。

因此，组件对于您的项目来说是完全独特的，没有混乱的 CSS 覆盖，没有使项目膨胀的未使用的样式，并且它们处理可访问性部分，所以您不必这样做。

#### 用法:

首先，您可以使用:
将 Vuetensils 安装到您的项目中

```
npm install vuetensils 
```

仅导入应用程序所需的组件。

您可以全局执行此操作:

```
// main.js
import Vue from "vue"
import { VAlert } from "vuetensils"

Vue.component("VAlert", VAlert) 
```

或者本地注册:

```
<script>
  // SomeComponent.vue
  import { VAlert } from "vuetensils"

  export default {
    components: {
      VAlert,
    },
    // ...
  }
</script> 
```

使用模板中的组件:

```
<template>
  <div class="some-component">
    <VAlert>Hey, I'm an alert!</VAlert>
  </div>
</template> 
```

自带风格:

```
/* Some CSS file */
.vts-alert {
  border: 1px solid currentColor;
  border-radius: 4px;
  padding: 0 10px;
  color: #900;
  background: #fdd;
} 
```

结果是一个 alert 元素，它具有很好的 ARIA 属性和很好的功能，并带有您自己的定制样式:

[![Example Vuetensils alert with styles](img/b07b4fb2f450f17439fe86adc455f305.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Oxr7Y8B0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kwvesc373hu7m29ze5uo.png)

## 结束语

从“裸”组件开始实际上为这个库打开了许多有趣的选项。

*   构建可访问的网站或应用程序(基本使用)。
*   用于 Vuetensils 的主题设计。
*   扩展组件以创建您自己的自定义、可访问的 UI 库。

Vuetensils 是我挠自己痒痒的结果。我希望我的项目有完全自定义的风格，并且也是可访问的，但是我不想从头开始构建一切，我也不想有一堆未使用的代码。

因此，它可能不适合所有人。

你们可能没有相同的需求，这完全没关系。使用我上面提到的第三方库没有任何问题。其实那些都是很优秀的项目。它们只是满足了不同的需求。

我希望这种事情能够发生，人们会更加关注让所有人都能访问网络。理想情况下，这使得开始这样做更容易，也许“带来你自己的风格”的概念会被接受。
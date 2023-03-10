# TailwindCSS 和 Vue -天作之合

> 原文：<https://dev.to/drewtownchi/tailwindcss-and-vue-a-match-made-in-heaven-34l0>

在诸如 Bootstrap、Foundation 和布尔玛等 CSS 框架的时代，重点总是放在组件上。框架将提供模态、表格、标签、导航菜单、下拉菜单以及结构化布局系统和偶尔的助手类。这种模式工作得很好，因为它让开发人员在必要的地方使用简单的`<div class="modal">Modal content</div>`。总会偶尔出现覆盖样式或与框架布局冲突的问题，但这是一个适用于许多人的系统。

多年来，Web 开发一直在不断发展，组件驱动开发的兴起非常流行。前端和后端网络技术，如 Vue，React，甚至 ASP.NET 核心已经采用并帮助传播模块化组件的思想。在 web 开发中，关注点分离和单一责任的思想已经从一种技术(CSS、HTML 或 js)转变为一个组件。

## 进入效用优先的 CSS 框架

实用优先框架并不新鲜。超光速粒子在 2014 年末出现，并获得了狂热的追随者。这并不是说它没有批评者。许多人哀叹实用至上的风格，称之为 HTML 中肮脏的 CSS，不同意简洁的类名。很有可能超光速粒子在错误的时间出现在了错误的地方。

2017 年 7 月，Adam Wathan 和 Jonathan Reinink 在 Steve Schoger 的设计下诞生了 TailwindCSS。Tailwind 的受欢迎程度迅速上升，赢得了开发人员的关注，并超越了 Tachyons，成为首选的实用 CSS 框架。

Tailwind 有一个漂亮的设计系统，易于记忆的类，并且紧密地映射到底层的 CSS。如果你还没有尝试过 Tailwind，你应该尝试一下，看看你是否喜欢它提供的小包裹大小，同时让你感觉非常强大。现在是时候尝试一下了...

## 为什么是现在？

对实用优先的 CSS 的最大抱怨之一是有许多重复的 CSS 类。让我们举一个*非常*简单的按钮例子

```
 <button
      type="button"
      class="bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded tracking-wider">
      Click me!
    </button> 
```

Enter fullscreen mode Exit fullscreen mode

我能听到你在想，“怎么回事？！我永远不会在每次想用按钮的时候输入所有这些类。”我不怪你。没有人会想输入那种卑鄙的 CSS 类，那太多了。每次你想改变网站上的按钮时，你都需要找到按钮的每个实例。然后，改变所有需要调整的类。

Tailwind 确实提供了一种通过`@apply`指令在构建时对这些类进行分组的方法。因此，你的那些聚集在一起创建一个按钮的类被简单地重命名为`.btn`

```
.btn {
  @apply bg-blue-600 text-white font-bold py-2 px-4 rounded tracking-wider;
}

.btn:hover {
  @apply hover:bg-blue-700
} 
```

Enter fullscreen mode Exit fullscreen mode

这是更好的，它给了我们一种方法来引用我们的按钮和一个单一的，集中的位置来更新 CSS，如果我们需要作出改变。比找一大堆不同的按钮好多了。

## 一个按钮仅仅是一个按钮吗？

很少一个按钮只是一个按钮，或者一张卡片只是一张卡片。无论是加载状态、错误状态、自定义错误消息、图标的批次，还是发送到父级的事件，总有一些定制的需求。按钮绝不仅仅是按钮。

与其使用`@apply`，为什么不利用现代开发的力量，在 Vue.js 组件中创建一个我们的`<custom-button>`组件。

在 HTML 中，我们可以直接在按钮类上定义静态类。但是对于任何基于属性或某种动态的东西，我们将使用计算属性，在本例中是`computedClasses`。我们还引入了一个可选的图标槽，使用`slot`语法。

```
<template>
  <button
    type="button"
    class="text-white font-bold py-2 px-4 rounded tracking-wider my-1"
    :class="computedClasses">
    <span class="whitespace-no-wrap">
      {{value}}
      <slot name="icon"></slot>
    </span>
  </button>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

在我们的 js 中，我们设置了一些道具，让消费者可以使用我们的自定义按钮。`Value`、`error`和`loading`是例子，但是可以有许多其他的可能性，例如尺寸约束、颜色、轮廓或填充。

接下来，我们设置一个 computed 属性，根据 prop 值，如果按钮处于错误状态，将背景更改为红色。

```
<script>
export default {
  props: ["value", "error", "loading"],
  computed: {
    computedClasses() {
      return {
        "bg-red-600 hover:bg-red-700": this.error,
        "bg-blue-600 hover:bg-blue-700 ": !this.error
      };
    }
  }
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

现在有了`<custom-button>`文件，我们可以从应用程序的任何地方调用这个按钮。

```
 <custom-button value="Hello, world!">
      <template #icon>😊</template>
    </custom-button>

    <custom-button value="Hello, world!" :error="true">
      <template #icon>😢</template>
    </custom-button> 
```

Enter fullscreen mode Exit fullscreen mode

我们获得了使用实用优先 CSS 库的所有好处，同时很好地将 Tailwind 类封装在一个组件中。这种模式非常有用，可以让您创建高度可定制的组件，满足您的需求，而不是开发 CSS 框架的人。HTML、CSS 和 js 现在在一个组件中，有一个职责，就是你的按钮。

你可以找到这篇文章以及更多类似的文章，包括更多关于顺风和 Vue 的文章
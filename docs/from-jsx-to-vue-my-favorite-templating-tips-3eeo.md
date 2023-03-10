# 从 JSX 到 Vue:我最喜欢的模板技巧

> 原文：<https://dev.to/briwa/from-jsx-to-vue-my-favorite-templating-tips-3eeo>

由于使用过 JSX，我对 Vue 的内置模板功能感到惊讶。由于我是后期采用者之一，这些对你来说可能并不新鲜，但我希望我在开始迁移到 Vue 时就知道这些额外的好处。所以，希望这能帮助那些正在迁移、尝试 Vue 或者考虑使用 Vue 的人。

无论如何，这里有一些我最喜欢的。

## 1。用于条件渲染的`v-if`、`v-else`和`v-else-if`

在组件中，对于要呈现的模板，总会有一些条件。我了解到`v-if`可以在提供条件的同时切换要渲染的元素。例如:

```
<!-- Basket.vue -->
<section id="the-one-fruit-to-buy">
  <orange v-if="sourFruitSelected && isExpensive" />
  <grape v-if="sourFruitSelected && !isExpensive" />
  <apple v-if="!sourFruitSelected">
</section> 
```

在这种情况下，我只想一次显示一个组件。如果`sourFruitSelected`和`isExpensive`为真，`orange`将只显示。从逻辑上看，这在浏览器上应该没问题。但是，如果你查看 HTML，你会看到这样的内容:

```
<!-- Inspecting the HTML -->
<section id="the-one-fruit-to-buy">
  <div id="orange-component"></div>
  <!----->
  <!----->
</section> 
```

两个元素用`<!----->`呈现，因为`v-if`语句是`false`。这是因为每个`v-if`对于它所针对的元素来说都是一个单独的条件。如果我使用三个`v-ifs`，Vue 不会知道三个元素中只有一个应该被显示，这是三个不同元素的三个条件。所以我要结合`v-else-if`和`v-else` :

```
<!-- Basket.vue -->
<section id="the-one-fruit-to-buy">
  <orange v-if="sourFruitSelected && isExpensive" />
  <grape v-else-if="sourFruitSelected && !isExpensive" />
  <apple v-else>
</section> 
```

这意味着切换三个元素只是一个条件。在 HTML 中，它看起来像这样:

```
<!-- Inspecting the HTML -->
<section id="the-one-fruit-to-buy">
  <div id="orange-component"></div>
</section> 
```

但是等等，还有呢！虽然条件语句是有效的，但很难理解。我们如何避免`sourFruitSelected`和`isExpensive`逻辑重复？`<template>` tag 可以帮你搞定。

`v-if`是一个指令，所以它只作用于单个元素。如果我们把 [`v-if`放到一个`<template>`](https://vuejs.org/v2/guide/conditional.html#Conditional-Groups-with-v-if-on-lt-template-gt) 中，它是一个空的元素包装器，我们可以如下改进逻辑:

```
<section class="the-one-fruit-to-buy">
  <template v-if="sourFruitSelected">
    <grape v-if="isExpensive">
    <orange v-else />
  <template>
  <apple v-else>
</section> 
```

## 2。`<component>`标记以避免相似组件的重复

假设我正在用 Vue 构建一个图表组件，看起来像这样:

```
<!-- Chart.vue -->
<chart
  :width="width"
  :height="height" /> 
```

随着时间的推移，我意识到我需要更多类型的图表，为每种类型构建不同的图表，所以代码现在看起来像这样:

```
<!-- Chart.vue -->
<pie-chart
  v-if="type === 'pie'"
  :width="width"
  :height="height" />
<bar-chart
  v-else-if="type === 'bar'"
  :width="width"
  :height="height" />
<column-chart
  v-else-if="type === 'column'"
  :width="width"
  :height="height" /> 
```

你注意到有某种模式，对吗？有了 [`<component>`](https://vuejs.org/v2/api/#component) 标签，我可以这样改进模板:

```
<!-- Chart.vue -->
<component
  :is="`${type}-chart`" 
  :width="width"
  :height="height" /> 
```

这基本上意味着它将呈现一个在`:is`下定义的名称为`${type}-chart`的组件。由于所有组件共享相同的道具(`width`和`height`)，两者都将被传递给每个渲染组件。

请注意，所有可能的组件都必须在父组件中注册(或全局安装)，这样才能工作。

## 3。`<slot>`共享组件可重用性标签

假设我正在实现一个模态组件，我会这样使用它:

```
<!-- Parent.vue -->
<modal :text="'Lorem ipsum'" /> 
```

实际的组件应该是:

```
<!-- Modal.vue -->
<div class="modal">
  {{ text }}
</div> 
```

然后，我想在模态中添加实际的 HTML 元素，比如表单。不仅仅是简单的文字。如何将字符串 prop 解析为 HTML？

使用`<slot>`，您可以将任何内容从父组件放置到组件的模板中。你不应该使用道具。因此，模态组件模板应该是:

```
<!-- Modal.vue -->
<div class="modal">
  <slot />
</div> 
```

使用模态的父组件应该是:

```
<!-- Parent.vue -->
<modal>
  <form>
    <h3>Do you want to delete these files permanently?</h3>
    <button>Cancel</button>
    <button>Delete</button>
  </form>
</modal> 
```

我认为 slots 是 Vue 中最有趣的特性之一，它将组件的可重用性提升到了一个全新的水平。请务必查看[文档](https://vuejs.org/v2/guide/components-slots.html)以了解更多详细信息。

## 4。`.camel`用于特定 camel-case HTML 属性的属性修饰符

有时，HTML 要求某些属性用骆驼字体书写。考虑这个例子:

```
<!-- Svg.vue -->

  <circle cx="0" cy="0" r="50"/>
 
```

`viewBox`用烤肉串写是因为[是风格指南](https://vuejs.org/v2/style-guide/#Prop-name-casing-strongly-recommended)推荐的道具。我以为 Vue 会知道，并转换回骆驼案件反正。

嗯，不会的！它将按原样呈现。你必须明确地告诉 Vue，这个属性在呈现时需要是骆驼大小写。你可以用 [`.camel`](https://vuejs.org/v2/api/#v-bind) :
来解决这个问题

```
<!-- Svg.vue -->

  <circle cx="0" cy="0" r="50"/>
 
```

`viewBox`将在 HTML 中正确呈现。

## 5。模板中廉价静态内容的指令

永远不会改变的静态内容可以通过在内容的根上添加`v-once`来优化，这样无论如何都只呈现一次。医生和 T2 说明了一切。

我认为这是 Vue 中提到最少的特性之一，因为我不认为静态内容会成为 Vue 用户的主要性能瓶颈。但是，如果你感兴趣，你可以使用它们，并让我知道它是如何进行的。或者，如果有人使用`v-once`获得了显著的性能提升，也请告诉我，我很乐意学习。

## 值得注意的提及

我还想提一下 [`v-model`](https://vuejs.org/v2/guide/forms.html) ，这是一个在父节点和子节点之间的数据通信模型，以及 [`<transition>`](https://vuejs.org/v2/guide/transitions.html) ，这是一个用于在元素之间转换的内置组件，但是我认为由于它们的健壮性和要涵盖的细节，每一个都值得写一篇文章。敬请期待！

* * *

当然，我并不是说 JSX 不能做 Vue 做的事情。我认为有了 JSX，一切皆有可能，因为它只是 Javascript。这篇文章的重点是更容易使用 Vue 的内置来实现 JSX 能做的(或者更好？).我必须说，JSX 有它自己的美，尤其是打字检查。在其他新闻中，你仍然可以使用 JSX 和 Vue！

顺便说一句，如果你有自己喜欢的 Vue 技巧，但没有在这里介绍，或者如果你对这些技巧有任何反馈，请在下面的评论中分享。此外，这些技巧已经包含在他们的官方文档中，只是想与我自己的用例分享一下。

感谢您的阅读！

* * *

封面图片由[杰弗里·汉密尔顿](https://unsplash.com/@pistos?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/migrate?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄。
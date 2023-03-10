# Vue 无渲染组件乐趣:谷歌分析事件跟踪器

> 原文：<https://dev.to/bryanleetc/vue-renderless-component-fun-google-analytics-event-tracker-5e2p>

假设您需要使用 Google Analytics 来跟踪 Vue 应用程序中某个特定按钮的点击次数，您会怎么做？首先想到的可能是点击处理程序。

```
<button @click="onClick">Important Button</button> 
```

```
methods: {
  onClick(e) {
    ga('send', 'event', 'Video', 'play')
    // Other things the button does when clicked
  }
} 
```

这是一个非常好的方法，但是当你开始跟踪更多的按钮和链接点击时，这就变得很麻烦了。使用 Vue 中的无渲染组件，我们可以将事件跟踪抽象成自己的易于重用的组件。

## 什么是无渲染组件

一个普通的 Vue 组件有一个 HTML 模板，Javascript 和 CSS。无渲染组件没有自己的标记。它通常有一个呈现作用域槽的呈现函数。来自无呈现组件的数据和函数通过作用域槽传递给子组件。这个简单的概念本质上让您能够抽象出复杂的行为，这些行为可以在不同的组件中重用。

为了更好地说明这一点，我们将构建一个无渲染组件，您可以包装按钮、链接和其他元素来跟踪谷歌分析中的点击。让我们从搭建一个我们称之为`Track`的无渲染组件开始。

```
// Renderless component
// Track.js
export default {
  render() {
    return $this.$slots.default;
  }
} 
```

```
// App.vue
<template>
  <div>
    <track>
      <button @click="onClick">Important Button</button>
    </track>
  </div> </template> 
<script>
import Track from 'track';

export default {
  components: { Track },
  methods: {
    onClick(e) {
      // the button's own click handler function
    }
  }
}
</script> 
```

我们将新的`Track`组件导入到我们的`App`组件中，并将其包装在我们想要跟踪的`<button>`元素周围。`Track`这里是一个非常基本的无渲染组件。它所做的只是呈现一个默认的槽，在这个例子中，呈现我们已经包装好的`<button>`。如果你现在刷新你的页面，你不会注意到任何不同。组件透明地呈现子按钮，它没有自己的标记。

## 听点击声

现在，我们想开始让我们的`Track`组件变得有用。我们希望它监听其子元素上的点击，然后在有点击时发送调用 Google Analytics API。

```
// Track.js
export default {
  render() {
    return $this.$slots.default;
  },
+ mounted() {
+   this.$slots.default[0].elm.addEventListener('click', this.trackClick, true);
+ },
+  methods: {
+    trackClick() {
+      ga('send', 'event', 'Video', 'play');
+    }
+  } } 
```

让我们过一遍我们刚刚做的。我们在组件的`mounted`生命周期钩子中声明我们正在添加一个事件监听器。`this.$slots.default`意味着我们正在访问组件的默认插槽。我们只寻找第一个元素，并为`click`事件添加一个事件监听器。如果有点击，我们运行`trackClick`功能。

`addEventListener`中的最后一个参数为真，表示我们希望使用事件捕获而不是默认的事件冒泡。事件捕获意味着事件从 DOM 树的顶部向下分派给我们的侦听器，而不是默认的从 DOM 树向上冒泡。实际上，即使声明了一个`preventDefault`,这也能让我们听到点击声。

## 使其更加可重复使用

还有一个小问题。如果我们想跟踪另一个页面中的另一个链接呢？再看看我们的代码，所有的点击都会调用这个`ga('send', 'event', 'Video', 'play')`。让我们这样做，以便我们可以自定义不同的变量，我们想发送到谷歌分析(GA)使用道具。我们也将遵循由 [GA API](https://developers.google.com/analytics/devguides/collection/analyticsjs/events) 设定的标准字段。

```
// Track.js
export default {
  render() {
    return $this.$slots.default;
  },
+ props: [
+   eventCategory,
+   eventAction,
+   eventLabel,
+   eventValue,
+ ],
  mounted() {
    this.$slots.default[0].elm.addEventListener('click', this.trackClick, true);
  },
  methods: {
    trackClick() {
-     ga('send', 'event', 'Video', 'play'); +     ga('send', 'event', eventCategory, eventAction, eventLabel, eventValue);
    }
  }
} 
```

现在，我们可以用相关的事件字段在不同的地方使用它:

```
<Track eventCategory="Checkout" eventAction="Button Click" eventLabel="Sidebar" eventValue="$30">
  <button>Some button</button>
</Track> 
```

## 快乐追踪！

我们的`Track`组件现在可以在代码的其他部分使用了。抽象出外部 API 的一个好处是代码现在变得更易于维护。Google 对需要更新的 GA API 进行了更新？不用更新 30 个使用 API 调用的不同地方，只需在`Track`中更新即可。

另一个巨大的好处是，你的代码库更具包容性。以前，加入您团队的新开发人员除了 Vue 技能之外，还需要熟悉 GA API。将此抽象成大多数 Vue 开发人员已经熟悉的直观概念，从第一天起就等于更高的生产力。

*这篇文章最初出现在[布莱恩·李](https://www.bryanleetc.com/vue-renderless-component-fun-google-analytics-event-tracker/)上。*
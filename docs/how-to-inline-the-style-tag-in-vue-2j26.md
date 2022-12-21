# 如何在 Vue 中内嵌样式标签

> 原文：<https://dev.to/plop/how-to-inline-the-style-tag-in-vue-2j26>

比方说，你想把某个伪元素作为目标，给它一个动态的`color`之类的东西。但是你有来自 Vue 值...

*   你不能使用 CSS，你不能访问`color`值
*   不能在伪元素上使用`v-bind:style`
*   Vue 不允许你在模板标签中使用`<style></style>`，vue-loader 或者 vue-template 会过滤掉任何样式标签

### 解决方案:*制作一个小组件输出一个样式标签*

> main.js

```
Vue.component('v-style', {
  render: function (createElement) {
    return createElement('style', this.$slots.default)
  }
}) 
```

> Component.vue(在你的`<template></template>` )

```
<v-style type="text/css">
  .progress::-webkit-progress-value { background-color: {{ color }}!important; }
</v-style> 
```

在这个解决方案中，我不得不使用`!important`，我看到一些人使用 unique _uid 来正确地使用`<component is="style">`定位元素，但是这个似乎更快👌
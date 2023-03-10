# 将所有道具传递给 Vue 中的儿童

> 原文：<https://dev.to/jwkicklighter/pass-all-props-to-children-in-vue-9on>

*这篇文章也发表在我的[博客](https://jwkicklighter.com/posts/pass-all-props-to-children-in-vue)上。*

在对一些新的 Vue 组件进行头脑风暴时，我希望有一种方法，使单个包装器组件可以获取子视图所需的所有数据，并将这些数据通过(大约 5 层)子视图向下传递到底部。

所以我做了一些实验，这里有一种方法可以将所有的组件道具传递给子组件。[链接到代码笔](https://codepen.io/jwkicklighter/pen/zYOBvmr)。

Vue 实际上让我们很容易做到这一点。所有的组件道具都可以在`$props`对象中获得(如果你不在模板中，就在`this.$props`中获得)，我们可以使用 Vue 指令`v-bind`而不用指定一个特定的道具名称，以便将整个道具对象绑定到子对象。

## 举例

让我们假设我们有一些包装组件，它将呈现一个子组件`comp1`，并将它作为道具传递给`propForComp1`和`propForComp2`。

这里是`Comp1.vue` :

```
<template>
  <div class="comp1">
    <span>{{ propForComp1 }}</span>
    <comp2 v-bind="$props" />
  </div>
</template>

<script>
  export default {
    components: Comp2,
    props: [
      'propForComp1',
      'propForComp2'
    ],
  }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`comp1`只是在使用`propForComp1`，并不真正关心`propForComp2`。但是由于需要包含在传递给`comp2`的道具中，`propForComp2`还是需要在`props`对象内部声明。如果没有，`$props`将不会包含它。

你可以对许多级别的组件这样做，但是记住任何子组件需要的道具**必须在**所有**父组件中声明。所以如果你有 5 层，`propForComp5`必须在`prop1`、`prop2`、`prop3`、`prop4`、`prop5`中声明为道具。你可以通过使用一个 Mixin 来使这变得简单一点，这是我在 [codepen](https://codepen.io/jwkicklighter/pen/zYOBvmr) 中选择的路线。**

**更新**:你其实不用做这最后一点！就像 Vue 放弃`$props`一样，`$attrs`对象包含了组件**没有**声明为道具的所有传递的属性。这意味着，我们可以简单地使用`v-bind="$attrs"`来传递孩子关心的属性，即使组件本身没有指定它们。

前面的例子会变成:

```
<template>
  <div class="comp1">
    <span>{{ propForComp1 }}</span>
    <comp2 v-bind="$props" v-bind="$attrs" />
  </div>
</template>

<script>
  export default {
    components: Comp2,
    props: [
      'propForComp1'
    ],
  }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

一个差异来看变化:

```
<template>
  <div class="comp1">
    <span>{{ propForComp1 }}</span>
-   <comp2 v-bind="$props" /> +   <comp2 v-bind="$props" v-bind="$attrs" />
  </div>
</template> 
<script>
  export default {
    components: Comp2,
    props: [
      'propForComp1',
-     'propForComp2'
    ],
  }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

感谢 [@morficus](https://dev.to/morficus) 指出这一点！
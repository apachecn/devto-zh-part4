# 如何迁移到 Vue 2.6

> 原文：<https://dev.to/darrenjennings/how-to-migrate-to-vue-2-6-1443>

我最近将我们工作中的静态 vue 应用从 2.x -> 2.6 进行了迁移。以下是一些可能对你有帮助的注意事项:

## 将您的依赖项更新到 2.6

```
yarn add vue-template-compiler@^2.6.10 vue@^2.6.10 
```

## 配合 Vue 2.6 使用 Vuepress

当 Vuepress 和 Vue 在同一个`package.json`时，有一些包版本冲突需要注意。如果你有 Vuepress ^1.0.2，你会看到这种错误:

```
Error:

Vue packages version mismatch:

- vue@2.6.10
- vue-server-renderer@2.5.22 
```

为了解决这个问题，我们可以使用`yarn`的一个特性。

### 纱线分辨率

纱线有一个特性叫做[纱线分辨率](https://yarnpkg.com/lang/en/docs/selective-version-resolutions/)。当一个包需要解析成一个特定的版本时，这允许你在你的`package.json`中声明明确的版本。

移除`vue`、`vue-template-compiler`，如果您的依赖项或 devDependencies 中碰巧有它们(可能不太可能)，移除`vue-server-renderer`和`@vuepress/core`。然后在你的包中添加一个`resolutions`字段

```
"resolutions":  {  "vue":  "^2.6.10",  "vue-template-compiler":  "^2.6.10",  "vue-server-renderer":  "^2.6.10",  "@vuepress/core":  "1.0.2"  }, 
```

## 更新你的槽模板语法

虽然槽语法变得简单多了，但它是完全向后兼容的。这不是一个必要的改变，但是我建议在您的使用插槽的组件上尝试一下。

这是一个无渲染组件，它传递了一些切换功能:

```
<!-- ❌ Old way -->
<KToggle>
  <template slot-scope="{ isToggled, toggle }">
    <KButton @click="toggle">
      {{ isToggled ? 'toggled' : 'not toggled' }}
    </KButton>
  </template>
</KToggle>

<!-- ✅ >= 2.6 -->
<KToggle v-slot="{ isToggled, toggle }">
  <KButton @click="toggle">
    {{ isToggled ? 'toggled' : 'not toggled' }}
  </KButton>
</KToggle> 
```

下面是一个命名槽的例子:

```
<!-- ❌ Old way -->
<Header>
  <template slot="title">
    <h1>User 2</h1>
  </template>
  <template slot="actions" slot-scope="{canEdit}">
    <a v-if="canEdit" href="/users/2/edit">Edit</a>
  </template>
</Header>

<!-- ✅ >= 2.6 -->
<Header>
  <template #title> <!-- or v-slot:title-->
    <h1>User 2</h1>
  </template>
  <template #actions="{canEdit}"> <!-- or v-slot:actions="{canEdit}"-->
    <a v-if="canEdit" href="/users/2/edit">Edit</a>
  </template>
</Header> 
```

## 使用渲染功能检查组件

槽的返回值现在是*总是保证是一个数组或者是未定义的*。这个[最近咬了我一口](https://github.com/darrenjennings/vue-autosuggest/pull/112)所以我现在做的一件事就是测试多个版本的 Vue 来捕捉这样的 bug。

```
const Child = {
  render(h) {
    // in >2.6 this will always return an Array or undefined. Before, you could
    // get a single VNode or an Array of multiple VNodes and you would need to do
    // extra validations on the slots.
    return this.$scopedSlots.default({})
  }
} 
```

2.6 中还有一些其他的变化，但是在这篇文章中，我想把重点放在我认为有帮助的事情上，并写下我在迁移到 2.6 时想读的文章。

链接:

*   尤雨溪 2.6 版上的文章[链接](https://medium.com/the-vue-point/vue-2-6-released-66aa6c8e785e)
*   插槽上的文档[链接](https://vuejs.org/v2/guide/components-slots.html#ad)
*   [要点](https://gist.github.com/yyx990803/f5cba7711ab57b5d0dd1f8261ebee278)解释作用域槽返回值

感谢核心团队对我的帮助:

*   [sodatea](https://github.com/sodatea) 为 [vue-cli](https://github.com/vuejs/vue-cli/blob/cc66247950cf81552f4e50a934d60fa361bf7351/package.json#L77-L83) repo 中的现有技术。
*   Edd Yerburgh 为[现有技术](https://github.com/vuejs/vue-test-utils/blob/dev/scripts/test-compat-all.sh)测试多个版本的 vue。
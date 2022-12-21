# 为你的 Nuxt 应用添加黑暗模式支持

> 原文：<https://dev.to/ericbrookfield/add-dark-mode-support-to-your-nuxt-app-3m0l>

我们正在将我们在 Vue 中的内部 CRM 重构为一个 Nuxt 应用程序，由于现在是 2019 年，我希望尽可能轻松地在整个应用程序中支持黑暗模式。

到目前为止，黑暗模式在 Safari、Firefox 和 Chrome 中得到了广泛的支持，在任何 web 应用程序上，只要使用 CSS 和 CSS 变量 T1 就可以完成很多事情。我的黑暗模式样式的 90%以上是在 CSS 中完成的，这是应该的。

但是如果你使用的是 Vue 这样的框架，你可能会直接用 props 来控制你的组件的一些风格，而不是仅仅依靠 CSS 类，就像这样:

```
/* Normal button for light mode */
<sui-button primary icon="plus">New entity</sui-button>

/* Inverted button for dark mode */
<sui-button primary inverted icon="plus">New entity</sui-button> 
```

我正在为我的按钮使用[语义用户界面](https://semantic-ui-vue.github.io/#/elements/button)，这……现在很好。如果我通过了`inverted="true"`(这里简称为`inverted`)，我就会得到我的黑暗模式友好按钮。

在我的 Nuxt 应用中，要将布尔值转化为全局可访问的变量，Vuex 商店似乎是一个正确的决定:

```
/* store/const.js */
export const state = () => ({
  darkMode: false
});
export const mutations = {
  setDarkMode: state => {
    state.darkMode = true;
  },
  unsetDarkMode: state => {
    state.darkMode = false;
  }
};
export const actions = {
  setDarkMode: ({ commit }) => commit("setDarkMode"),
  unsetDarkMode: ({ commit, state }) => state.darkMode && commit("unsetDarkMode")
}; 
```

假设您设置了一些黑暗模式媒体查询样式，检查黑暗模式并监听更改是我们可以在布局文件中做的事情:

```
/* layouts/default.vue */
<template>
 <nuxt />
</template>
<script>
export default {
  components: {
    AdminNav
  },
  data() {
    return {
      mql: window.matchMedia('(prefers-color-scheme: dark)')
    }
  },
  created() {
    this.darkMode(this.mql)
    this.mql.addListener(this.darkMode)
  },
  beforeDestroy() {
    this.mql.removeListener(this.darkMode)
  },
  methods: {
    darkMode: function(e) {
      if (e.matches) {
        return this.$store.dispatch('const/setDarkMode')
      }
      return this.$store.dispatch('const/unsetDarkMode')
    }
  }
}
</script> 
```

最后，在任何需要的组件中获取布尔值只需要一个计算属性:

```
/* components/myComponent.vue */
<template>
  <sui-button primary inverted="darkMode" icon="plus">New entity</sui-button>
</template>

<script>
export default {
  computed: {
    darkMode() {
      return this.$store.state.const.darkMode
    }
  }
};
</script> 
```

现在，当我使用黑暗模式时，我得到了漂亮的倒置按钮。妙的😋。
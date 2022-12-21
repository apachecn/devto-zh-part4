# 异步请求的 Vue.js 模式:使用无渲染组件

> 原文：<https://dev.to/lhermann/vue-js-pattern-for-async-requests-using-renderless-components-3gd>

大多数 Vue 应用需要异步 HTTP 请求，并且有许多方法可以实现它们:在`mounted()`生命周期钩子中，在按钮触发的方法中，在商店中(当使用 [vuex](https://vuex.vuejs.org/) 时)或者在`asyncData()`和`fetch()`方法中(使用 [Nuxt](https://nuxtjs.org/) )。

虽然一个简单的请求在 axios 中非常容易，但我们通常希望至少覆盖两个附加状态:

1.  在请求挂起时向用户显示一些内容
2.  优雅地处理错误

处理这些状态会增加额外的代码，当必须实现许多不同的请求时，会很快导致代码重复。

# 目录

1.  [想法的由来](#origin-of-the-idea)
2.  [HTTP 请求:一个典型的例子](#http-request-in-vue-components-a-typical-example)
3.  [异步无渲染组件](#the-async-renderless-component)
    *   为什么不是 mixin 或 directive？
    *   [一个应用实例](#an-applied-example)

直接切入正题，跳到异步无渲染组件。

**注意:**在这个例子中，Axios 用于发出 HTTP 请求，但是它与任何其他用于 AJAX 请求的库一样工作良好。同样，这个例子使用了这个精彩的免费狗 API:【https://dog.ceo/dog-api/

# 想法的由来

这个想法不是我自己的，而是借用了 Vue.js 的创作者尤雨溪[@游鱼溪](https://twitter.com/youyuxi)在[第 81 集](http://www.fullstackradio.com/81)与亚当·沃坦在[全栈电台](http://www.fullstackradio.com/)播客上谈论高级 Vue 组件时的第二次发声。

# Vue 组件中的 HTTP 请求:典型例子

让我们从一个最小的例子开始，请求一个随机的狗图片。`mounted()`钩子包含填充`image`变量的 axios 调用。

```
Vue.component("example", {
  el: "#example",
  data() {
    return {
      image: null
    };
  },
  mounted() {
    axios
      .get("https://dog.ceo/api/breeds/image/random")
      .then(function(response) {
        this.image = response.data;
      });
  }
}); 
```

很简单。但是，我们希望显示一个加载动画并处理请求错误。所以除了`image`之外，还增加了变量`pending: false`和`error: null`。`mounted()`钩子看起来如下:

```
Vue.component("example", {
  [...]
  mounted() {
    this.pending = true;
    axios
      .get("https://dog.ceo/api/breeds/image/random")
      .then(function(response) { this.image = response.data })
      .catch(function(error) { this.error = error })
      .finally(function () { this.pending = false });
  }
}); 
```

现在可以为`pending === true`显示一个装载指示器，如果`error !== null`显示一个基本错误信息。这真的很简单，但是重复实现这种挂起/成功/错误行为会变得很乏味。此外，如果请求包含可由用户更改的参数，例如过滤器或排序选项，则请求必须移动到一个方法，每当参数更改时，该方法必须被调用以重新加载数据。

抽象出这个简单的行为并使其可重用的一个简单而有效的方法是...

# 异步无渲染组件

这个组件利用了极其通用的[作用域插槽特性](https://vuejs.org/v2/guide/components-slots.html#Scoped-Slots)。slot 是可以传递给组件的任何一段 HTML，告诉组件:“在这里，在某个地方呈现这个”。对于作用域槽，接收 HTML 剪切的组件回答:“太棒了，我会把你的 HTML 放在那里。这里有一些数据，如果你喜欢的话，可以用在你的剪报上。

异步无渲染组件就是这样一个组件，它接收一个 HTML 片段、一个 URL 和参数，并回答:“嘿，看，我正在为你请求这些数据，这里有`data`、`pending`和`error`供你使用。”

完整的异步无渲染组件:

```
Vue.component("async", {
  props: {
    url: { type: String, default: "", required: true },
    params: { type: Object, default: () => ({}) }
  },
  data() {
    return {
      pending: true,
      error: false,
      data: null
    };
  },
  watch: {
    url() {
      this.requestData();
    },
    params: {
      handler() {
        this.requestData();
      },
      deep: true
    }
  },
  mounted() {
    this.requestData();
  },
  methods: {
    async requestData() {
      this.pending = true;
      try {
        const { data } = await axios.get(this.url, { params: this.params });
        this.data = data;
        this.error = false;
      } catch (e) {
        this.data = null;
        this.error = e;
      }
      this.pending = false;
    }
  },
  render() {
    return this.$scopedSlots.default({
      pending: this.pending,
      error: this.error,
      data: this.data
    });
  }
}); 
```

***注意:**我在这里使用了一些 javascript 魔法:[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)、[异步/等待](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)和[尝试...接住](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch)。*

“无渲染”发生在`render()`标签中。这些组件并没有使用 HTML 标签，而是将它在自己的 slot 中接收到的 HTML 片段作为作用域 slot 来呈现，并向它传递三个数据点:`pending`、`error`和`data`。

`watch`功能确保每当`url`或`params`改变时，数据被重新加载。

我们像这样在模板中使用异步组件:

```
<async url="https://dog.ceo/api/breed/husky/images">
  <template v-slot:default="{ pending, error, data }">
    <div v-if="pending">Loading ...</div>
    <div v-else-if="error">{{ error }}</div>
    <div v-else>{{ data }}</div>
  </template>
</async> 
```

## 为什么是无渲染组件而不是 mixin 或指令？

组件并不是在 Vue 中重用代码的唯一方式，另一种方式是使用 [Mixin](https://vuejs.org/v2/guide/mixins.html) 或[自定义指令](https://vuejs.org/v2/guide/custom-directive.html)。两者都是解决这个问题的好方法。使用作用域插槽的无渲染组件以 Vue 想要的方式运行，它可以在需要时被导入，就像你习惯使用任何其他组件一样。因此，这是一种非常明确的重用代码的方式，而不是必须单独包含的混合或指令。最终，这归结为偏好。

## 一个应用实例

在使用 API 时，我经常发现自己在实现列表，这些 API 通常具有分页、过滤、排序和搜索等功能。所以我决定将一个“真实生活”的例子放在一起，这个例子呈现了一个简单的狗图像列表，并为一些不同的品种提供了一个非常简单的过滤器选项(以及一个错误的 API 调用来查看错误状态):

[https://codepen.io/lhermann/embed/jgGxyQ?height=600&default-tab=result&embed-version=2](https://codepen.io/lhermann/embed/jgGxyQ?height=600&default-tab=result&embed-version=2)

每当单击一个过滤器按钮时，传递给异步组件的 URL 就会用适当的品种进行更新。异步组件负责处理 HTTP 请求。在父组件中不再需要 HTTP 请求逻辑，关注点的分离得到了遵守，我们的思想得到了解放，宇宙处于和谐之中😄。
# Vue 多个逻辑主题，但是带有对象 API

> 原文：<https://dev.to/amcsi/vue-multiple-logic-topics-but-with-object-api-oho>

最近有一些关于即将到来的 VueJS 3 函数 API 的争论。它带来了几个好处，比如能够让库作者更容易地共享代码行为，以及更好的类型脚本支持。

当然，实现这一点有各种各样的缺点，有些人认为这增加了复杂性和不优雅的语法。

虽然我不想谈论这个新函数 API 的所有优点和缺点。

我只想谈谈函数 API 可能带来的一个好处，但我声称只需使用 VueJS 2 中可用的对象 API 就完全可以实现:

### 多个逻辑题目

根据[函数 API 文档](https://github.com/vuejs/rfcs/blob/function-apis/active-rfcs/0000-function-api.md#multiple-logic-topics)，虽然它的例子被简化了，但它提出了一个事实，即大型项目中的大型 VueJS 组件可以有多个逻辑发生的主题(即域),但很难区分哪些属性定义在数据、方法、计算等中。属于哪个话题。

下面是 VueJS 2 中混合鼠标移动跟踪和数据获取主题的例子。注意，这个例子是简化的；在更大的代码中，这可能是一个更大的问题。

```
<template>
  <div>
    <template v-if="isLoading">Loading...</template>
    <template v-else>
      <h3>{{ post.title }}</h3>
      <p>{{ post.body }}</p>
    </template>
    <div>Mouse is at {{ x }}, {{ y }}</div>
  </div>
</template> 
```

```
import { fetchPost } from './api'

export default {
  props: {
    id: Number
  },
  data() {
    return {
      isLoading: true,
      post: null,
      x: 0,
      y: 0
    }
  },
  mounted() {
    this.fetchPost()
    window.addEventListener('mousemove', this.updateMouse)
  },
  watch: {
    id: 'fetchPost'
  },
  destroyed() {
    window.removeEventListener('mousemove', this.updateMouse)
  },
  methods: {
    async fetchPost() {
      this.isLoading = true
      this.post = await fetchPost(this.id)
      this.isLoading = false
    },
    updateMouse(e) {
      this.x = e.pageX
      this.y = e.pageY
    }
  }
} 
```

下面是 VueJS 3 提出的借助于函数 API 的更干净的主题分离:

```
import { fetchPost, value, watch, onMounted, onUnmounted } from './api'

function useFetch(props) {
  const isLoading = value(true)
  const post = value(null)

  watch(() => props.id, async (id) => {
    isLoading.value = true
    post.value = await fetchPost(id)
    isLoading.value = false
  })

  return {
    isLoading,
    post
  }
}

function useMouse() {
  const x = value(0)
  const y = value(0)
  const update = e => {
    x.value = e.pageX
    y.value = e.pageY
  }
  onMounted(() => {
    window.addEventListener('mousemove', update)
  })
  onUnmounted(() => {
    window.removeEventListener('mousemove', update)
  })
  return { x, y }
}

export default {
  setup(props) {
    return {
      ...useFetch(props),
      ...useMouse()
    }
  }
} 
```

事实上，有人会说函数 API 给了你一个机会，把属于同一个主题的逻辑组织在一起，并打包到可重用的函数中。

然而，完全有可能用 VueJS 2 的老式对象 API 有效地做同样的事情。这需要在 VueJS 组件对象定义之前将主题代码写入变量:

```
import { fetchPost } from "./api"

const fetchData = {
  data: {
    isLoading: true,
    post: null
  },
  mounted() {
    this.fetchPost()
  },
  watch: {
    id: "fetchPost"
  },
  methods: {
    async fetchPost() {
      this.isLoading = true
      this.post = await fetchPost(this.id)
      this.isLoading = false
    }
  }
}
const mouseData = {
  data: {
    x: 0,
    y: 0
  },
  mounted() {
    window.addEventListener("mousemove", this.updateMouse)
  },
  destroyed() {
    window.removeEventListener("mousemove", this.updateMouse)
  },
  methods: {
    updateMouse(e) {
      this.x = e.pageX
      this.y = e.pageY
    }
  }
}
export default {
  props: {
    id: Number
  },
  data() {
    return {
      ...fetchData.data,
      ...mouseData.data
    }
  },
  mounted() {
    fetchData.mounted.call(this)
    mouseData.mounted.call(this)
  },
  watch: {
    ...fetchData.watch
  },
  destroyed() {
    mouseData.destroyed.call(this)
  },
  methods: {
    ...fetchData.methods,
    ...mouseData.methods
  }
} 
```

如你所见，主题被分成了`fetchData`和`mouseData`。然后，我们在对象 API 的每个属性中为拥有它的每个主题显式地进行对象传播。

起初，对象扩散可能看起来很奇怪，但是如果你仔细想想，它在 Vuex 的`mapXXX()`函数中很常用。

但是这里有一点代码重复。人们可能会忘记从某个主题的属性中扩展一些内容，或者包括调用 lifetime 方法，或者忘记绑定`this`。我想知道是否有一种方法可以简化这段代码，去掉许多样板文件...

```
import { fetchPost } from "./api"

const fetchData = {
  data: {
    isLoading: true,
    post: null
  },
  mounted() {
    this.fetchPost()
  },
  watch: {
    id: "fetchPost"
  },
  methods: {
    async fetchPost() {
      this.isLoading = true
      this.post = await fetchPost(this.id)
      this.isLoading = false
    }
  }
}
const mouseData = {
  data: {
    x: 0,
    y: 0
  },
  mounted() {
    window.addEventListener("mousemove", this.updateMouse)
  },
  destroyed() {
    window.removeEventListener("mousemove", this.updateMouse)
  },
  methods: {
    updateMouse(e) {
      this.x = e.pageX
      this.y = e.pageY
    }
  }
}
export default {
  props: {
    id: Number
  },
  mixins: [fetchData, mouseData]
} 
```

惊喜惊喜，这恰恰是 mixins 做的:d。

如果您以前没有想到这一点，您也可以定义 mixins 并在同一个文件中使用它们。在这种情况下，反对关于属性冲突的混合的常见论点不再那么有力，并且您不知道属性来自哪个文件。但是有人可能会说这段代码很好地分离了主题*并且*也非常干净简单。

### 关闭思绪

当然，你不能像使用函数 API 一样在“主题”之间共享行为(想想使用 React 钩子的`useEffect()`)。函数 API 还有其他好处。

我这篇文章的真正目的只是为了证明，关于新函数 API 带来分离主题能力的一个论点是*假的*，因为你可以用现有的对象 API 做同样的事情。
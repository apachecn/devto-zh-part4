# 维格斯死了，万岁维格斯！

> 原文：<https://dev.to/stefandorresteijn/vuejs-is-dead-long-live-vuejs-1g7f>

大约两周前，随着 VueJS 3“征求意见”文档的发布，尤雨溪推出了基于函数的 VueJS API，并在 vue js 社区引起了轰动。这些新想法仍处于“征求意见”阶段，因此它们还远未确定，但是因为 RFC 引入了如此重大的变化，所以我快速总结了您需要知道的内容。

注:所有这些信息以及更多信息都在 RFC 中，所以我建议你去阅读。

## 设置

VueJS 3 背离了我们喜欢的基于选项的 API，引入了`setup()`函数，这将是所有神奇事情发生的地方。这个函数单独为我们的组件设置逻辑，并返回暴露给模板的数据。基于选项的 API 即使在 VueJS 3 中也将继续工作，但是这个新的基于函数的 API 将成为新的标准。

对于我们习惯于从 VueJS 得到的所有功能，如反应数据、计算值、方法和观察器，我们从`vue`中得到`import`函数，并在我们的`setup()`函数中使用它们。这里有一个来自 RFC 的基本例子:

```
<template>
  <div>
    <span>count is {{ count }}</span>
    <span>plusOne is {{ plusOne }}</span>
    <button @click="increment">count++</button>
  </div>
</template>

<script>
import { value, computed, watch, onMounted } from 'vue'

export default {
  setup() {
    // reactive state
    const count = value(0)
    // computed state
    const plusOne = computed(() => count.value + 1)
    // method
    const increment = () => { count.value++ }
    // watch
    watch(() => count.value * 2, val => {
      console.log(`count * 2 is ${val}`)
    })
    // lifecycle
    onMounted(() => {
      console.log(`mounted`)
    })
    // expose bindings on render context
    return {
      count,
      plusOne,
      increment
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## 但是为什么呢？

如果这个例子不能清楚地说明为什么要引入这个变化，或者从可用性的角度看感觉像是倒退，我理解。我最初也有同样的反应，我花了一点时间才明白为什么这种改变是必要的。v2.x API 广受欢迎，并且经常是人们从 ReactJS 或 AngularJS 等其他框架转向 VueJS 的原因，因此如此剧烈的变化似乎是一个奇怪的想法。

### 封装为王

创建组件 API 的部分目的是为了更容易在应用程序中重用代码。虽然 VueJS 是非常模块化的，并且使用组件，但是当前基于选项的 API 不允许简单地提取与单个功能或数据相关的功能。您需要分别定义您的数据(/state)、计算值和方法，尽管它们可能都是相关的。当组件增长并且方法处理不同的数据时，这变得令人困惑。

这就是新的基于函数的 API 的用武之地。它允许你提取与一个逻辑相关的所有代码，并把它们放在一个他们称之为“组合函数”的地方，这个函数返回反应状态。RFC 中给出的一个例子使用其中一个组合函数来[提取监听鼠标位置的逻辑](https://github.com/vuejs/rfcs/blob/function-apis/active-rfcs/0000-function-api.md#logic-composition) :

```
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

// in consuming component
const Component = {
  setup() {
    const { x, y } = useMouse()
    return { x, y }
  },
  template: `<div>{{ x }} {{ y }}</div>`
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们将其与我们在 v2.x API 中编写的功能进行比较，我们可以看到与使用鼠标位置相关的功能无处不在，而在 v3.x API 中，它被很好地组合在一个单一的函数中:

```
<template>
    <div>
        {{ x }} {{ y }}
    </div>
</template>

<script>
export default {
  data() {
    return {
      x: 0,
      y: 0,
    };
  },
  mounted() {
    window.addEventListener('mousemove', this.update);
  },
  beforeDestroy() {
    window.removeEventListener('mousemove', this.update);
  },
  methods: {
    update(e) {
      this.x = e.pageX;
      this.y = e.pageY;
    },
  },
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

### 还有更多

封装并不是这些变化有用的唯一原因，所以这里有另外两个原因说明这种变化可能是 VueJS 所需要的。

VueJS 中当前基于选项的 API 存在一个问题，即它不能正确支持 TypeScript 类型推断。提议的更改解决了这个问题，并实现了完全的类型支持，TS 代码看起来几乎与 JS 代码一样，就像是已经非常有用的馅饼上的一颗樱桃。

VueJS 因其极小的包大小而广受欢迎，这一变化进一步缩小了包的大小。由于函数和变量名可以用标准的缩略法缩短(而对象/类方法和属性不能)，代码压缩得更好。

## 思想？

对 RFC 的最初反应不一，一些用户将这些变化与 React 进行比较，认为 VueJS 正在失去它的优势。我的第一个反应也远非正面，但是我看得越久，封装的优势就越开始超过当前 API 的整洁。

你有什么想法？
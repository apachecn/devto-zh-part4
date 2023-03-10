# 无视图 Vue 无渲染组件介绍

> 原文：<https://dev.to/ycmjason/vue-without-view-an-introduction-to-renderless-components-23ld>

[https://www.youtube.com/embed/j_WU0xx_O58](https://www.youtube.com/embed/j_WU0xx_O58)

随着组件变得越来越大，维护起来也越来越困难。有时，如何将臃肿的组件分割成更小的组件并不明显。代码变得更加嘈杂，变得难以理解。

在这篇文章中，我将介绍“无渲染组件”的概念，它有可能帮助你改进你的组件。

## 我的神奇网站

我们将调查[我的神奇网站](https://ycmjason-talks.github.io/2019-06-20-vue-js-london-meetup-11/)的[来源](https://github.com/ycmjason-talks/2019-06-20-vue-js-london-meetup-11)。(如果你不想剧透，先不要急着做公关。)

## Groovy 页脚

看到页面底部的 groovy 页脚了吗？让我们来看看这个页脚的源代码。

[src/components/footer . vue](https://github.com/ycmjason-talks/2019-06-20-vue-js-london-meetup-11/blob/master/src/components/Footer.vue):

```
<template>
  <footer :style="footerStyle">
    <div class="text" :style="textStyle">Made with ❤ by Jason Yu &copy; 2019</div>
    <label class="insane-mode-label">
      <input type="checkbox" v-model="insaneMode"> Insane Mode (new!)
    </label>
  </footer>
</template>

<script>
import { randomNumber, randomPercentage, randomColor } from '../services/random';

const FOOTER_INTERVAL_MS = 543;
const TEXT_INTERVAL_MS = FOOTER_INTERVAL_MS / 3;

export default {
  mounted() {
    this.randomFooterStyle();
    this.randomTextStyle();
    this.footerIntervalId = window.setInterval(this.randomFooterStyle, this.footerIntervalMs);
    this.textIntervalId = window.setInterval(this.randomTextStyle, this.textIntervalMs);
  },
  beforeDestroy() {
    window.clearInterval(this.footerIntervalId);
    window.clearInterval(this.textIntervalId);
  },
  data: () => ({
    footerStyle: null,
    textStyle: null,
    insaneMode: false,
  }),
  computed: {
    insaneFactor() {
      return this.insaneMode ? 3 : 1;
    },
    footerIntervalMs() {
      return FOOTER_INTERVAL_MS / this.insaneFactor;
    },
    textIntervalMs() {
      return FOOTER_INTERVAL_MS / this.insaneFactor;
    },
  },
  watch: {
    insaneMode() {
      window.clearInterval(this.footerIntervalId);
      window.clearInterval(this.textIntervalId);
      this.footerIntervalId = window.setInterval(this.randomFooterStyle, this.footerIntervalMs);
      this.textIntervalId = window.setInterval(this.randomTextStyle, this.textIntervalMs);
    },
  },
  methods: {
    randomFooterStyle() {
      const { insaneFactor } = this;
      this.footerStyle = {
        borderRadius: `${randomPercentage()}  ${randomPercentage()} / ${randomPercentage()}  ${randomPercentage()}`,
        background: randomColor(),
        transitionDuration: `${FOOTER_INTERVAL_MS / insaneFactor}ms`,
      };
    },
    randomTextStyle() {
      const { insaneFactor } = this;
      this.textStyle = {
        transform: `rotate(${randomNumber(
          -3 * insaneFactor,
          3 * insaneFactor,
        )}deg) scale(${randomNumber(0.7 * insaneFactor, 1.3 * insaneFactor)})`,
        color: randomColor(),
        transitionDuration: `${TEXT_INTERVAL_MS / insaneFactor}ms`,
      };
    },
  },
};
</script>

<style scoped>
footer {
  margin-top: 1rem;
  padding: 3rem 0;
  transition-property: border-radius, background;
  text-align: center;
}
footer .text {
  transition-property: color, transform;
}
.insane-mode-label {
  display: block;
  margin-top: 2rem;
}
</style> 
```

注意`<script>`中超过一半的代码是如何用来处理`window.setInterval`和`window.clearInterval`的。我们如何简化这个组件？将页脚文本和背景移动到它们自己的组件中是没有意义的，因为它们在语义上属于页脚而不是它们自己！

## <区间>

让我们创建一个名为`<Interval>`的组件，它将为我们处理与`window.setInterval`和`window.clearInterval`相关的所有事情。

src/components/renderless/interval . js:

```
export default {
  render: () => null,
}; 
```

首先，正如本文标题所示，`render`函数应该什么都不呈现。所以我们返回`null`。

### 道具

接下来，`<Interval>`应该接受什么样的道具？显然，我们希望能够控制每个区间之间的`delay`。

src/components/renderless/interval . js:

```
export default {
  props: {
    delay: {
      type: Number,
      required: true,
    },
  },
  render: () => null,
} 
```

### 挂载

当安装了`<Interval>`时，我们希望它开始间隔，并在`beforeDestroyed`时断开间隔。

src/components/renderless/interval . js:

```
export default {
  props: {
    delay: {
      type: Number,
      required: true,
    },
  },
  mounted () {
    this.id = window.setInterval(() => /* ... */, this.delay);
  },
  beforeDestroy () {
    window.clearInterval(this.id);
  },
  render: () => null,
} 
```

### 在`/* ... */`我们应该做什么？

`setInterval`接受两个参数，一个回调和一个延迟。那么我们是不是应该把`callback`收进来当道具呢？这是一个伟大的想法，可以很好地工作。但是我想说一种更“Vue-ish”的方式是发出事件！

src/components/renderless/interval . js:

```
export default {
  props: {
    delay: {
      type: Number,
      required: true,
    },
  },
  mounted () {
    this.id = window.setInterval(() => this.$emit('tick'), this.delay);
  },
  beforeDestroy () {
    window.clearInterval(this.id);
  },
  render: () => null,
} 
```

### 搞定！

虽然很简单，但它赋予了我们区间的力量，而不需要管理区间 id 和区间的设置/拆除！

## 重构 Footer.vue！

让我们分别处理 Footer.vue:
中`mounted`和`beforeDestroy`钩子中的`setInterval`和`clearInterval`

```
// ...
  mounted() {
    // ...
    this.footerIntervalId = window.setInterval(this.randomFooterStyle, this.footerIntervalMs);
    this.textIntervalId = window.setInterval(this.randomTextStyle, this.textIntervalMs);
  },
  beforeDestroy() {
    window.clearInterval(this.footerIntervalId);
    window.clearInterval(this.textIntervalId);
  },
// ... 
```

上面的代码现在可以替换为:

```
 <Interval :delay="footerIntervalMs" @tick="randomFooterStyle"></Interval>
   <Interval :delay="textIntervalMs" @tick="randomTextStyle"></Interval> 
```

生成的 Footer.vue 将类似于:

```
<template>
  <footer :style="footerStyle">
    <Interval :delay="footerIntervalMs" @tick="randomFooterStyle"></Interval>
    <Interval :delay="textIntervalMs" @tick="randomTextStyle"></Interval>
    <div class="text" :style="textStyle">Made with ❤ by Jason Yu &copy; 2019</div>
    <label class="insane-mode-label">
      <input type="checkbox" v-model="insaneMode"> Insane Mode (new!)
    </label>
  </footer>
</template>

<script>
import { randomNumber, randomPercentage, randomColor } from '../services/random';
import Interval from './renderless/Interval';

const FOOTER_INTERVAL_MS = 543;
const TEXT_INTERVAL_MS = FOOTER_INTERVAL_MS / 3;

export default {
  mounted() {
    this.randomFooterStyle();
    this.randomTextStyle();
  },
  data: () => ({
    footerStyle: null,
    textStyle: null,
    insaneMode: false,
  }),
  computed: {
    insaneFactor() {
      return this.insaneMode ? 3 : 1;
    },
    footerIntervalMs() {
      return FOOTER_INTERVAL_MS / this.insaneFactor;
    },
    textIntervalMs() {
      return FOOTER_INTERVAL_MS / this.insaneFactor;
    },
  },
  watch: {
    insaneMode() {
      window.clearInterval(this.footerIntervalId);
      window.clearInterval(this.textIntervalId);
      this.footerIntervalId = window.setInterval(this.randomFooterStyle, this.footerIntervalMs);
      this.textIntervalId = window.setInterval(this.randomTextStyle, this.textIntervalMs);
    },
  },
  methods: {
    randomFooterStyle() {
      const { insaneFactor } = this;
      this.footerStyle = {
        borderRadius: `${randomPercentage()}  ${randomPercentage()} / ${randomPercentage()}  ${randomPercentage()}`,
        background: randomColor(),
        transitionDuration: `${FOOTER_INTERVAL_MS / insaneFactor}ms`,
      };
    },
    randomTextStyle() {
      const { insaneFactor } = this;
      this.textStyle = {
        transform: `rotate(${randomNumber(
          -3 * insaneFactor,
          3 * insaneFactor,
        )}deg) scale(${randomNumber(0.7 * insaneFactor, 1.3 * insaneFactor)})`,
        color: randomColor(),
        transitionDuration: `${TEXT_INTERVAL_MS / insaneFactor}ms`,
      };
    },
  },
};
</script>

<style scoped>
footer {
  margin-top: 1rem;
  padding: 3rem 0;
  transition-property: border-radius, background;
  text-align: center;
}
footer .text {
  transition-property: color, transform;
}
.insane-mode-label {
  display: block;
  margin-top: 2rem;
}
</style> 
```

注意到组件看起来已经漂亮多了吗？不再有像`footerIntervalId`或`textIntervalId`这样可笑的名字，也不再需要担心忘记撕掉音程！

## 疯狂模式

疯狂模式由 Footer.vue:
中的守望者开启

```
<template>
   <!-- ... -->
   <Interval :delay="footerIntervalMs" @tick="randomFooterStyle"></Interval>
   <Interval :delay="textIntervalMs" @tick="randomTextStyle"></Interval>
   <!-- ... -->
</template>

<script>
// ...
  watch: {
    insaneMode() {
      window.clearInterval(this.footerIntervalId);
      window.clearInterval(this.textIntervalId);
      this.footerIntervalId = window.setInterval(this.randomFooterStyle, this.footerIntervalMs);
      this.textIntervalId = window.setInterval(this.randomTextStyle, this.textIntervalMs);
    },
  },
// ...
</script> 
```

很明显，我们希望移除这个观察器，并将逻辑移到`<Interval>`中。

当疯狂模式被触发时，`<Interval>`收到一个新的`delay`道具，因为`this.footerIntervalMs`和`this.textIntervalMs`被改变了。然而，`<Interval>`还没有被编程为对`delay`的变化做出反应。我们可以给`delay`添加一个观察器，它将拆除现有的间隔并建立一个新的间隔。

Interval.js

```
export default {
  props: {
    delay: {
      type: Number,
      required: true,
    },
  },
  mounted () {
    this.id = window.setInterval(() => this.$emit('tick'), this.delay);
  },
  beforeDestroy () {
    window.clearInterval(this.id);
  },
  watch: {
    delay () {
      window.clearInterval(this.id);
      this.id = window.setInterval(() => this.$emit('tick'), this.delay);
    },
  },
  render: () => null,
} 
```

现在我们可以移除 Footer.vue:

```
 watch: {
    insaneMode() {
      window.clearInterval(this.footerIntervalId);
      window.clearInterval(this.textIntervalId);
      this.footerIntervalId = window.setInterval(this.randomFooterStyle, this.footerIntervalMs);
      this.textIntervalId = window.setInterval(this.randomTextStyle, this.textIntervalMs);
    },
  }, 
```

最终的 Footer.vue 是这样的:

```
<template>
  <footer :style="footerStyle">
    <Interval :delay="footerIntervalMs" @tick="randomFooterStyle"></Interval>
    <Interval :delay="textIntervalMs" @tick="randomTextStyle"></Interval>
    <div class="text" :style="textStyle">Made with ❤ by Jason Yu &copy; 2019</div>
    <label class="insane-mode-label">
      <input type="checkbox" v-model="insaneMode"> Insane Mode (new!)
    </label>
  </footer>
</template>

<script>
import { randomNumber, randomPercentage, randomColor } from '../services/random';
import Interval from './renderless/Interval';

const FOOTER_INTERVAL_MS = 543;
const TEXT_INTERVAL_MS = FOOTER_INTERVAL_MS / 3;

export default {
  mounted() {
    this.randomFooterStyle();
    this.randomTextStyle();
  },
  data: () => ({
    footerStyle: null,
    textStyle: null,
    insaneMode: false,
  }),
  computed: {
    insaneFactor() {
      return this.insaneMode ? 3 : 1;
    },
    footerIntervalMs() {
      return FOOTER_INTERVAL_MS / this.insaneFactor;
    },
    textIntervalMs() {
      return FOOTER_INTERVAL_MS / this.insaneFactor;
    },
  },
  methods: {
    randomFooterStyle() {
      const { insaneFactor } = this;
      this.footerStyle = {
        borderRadius: `${randomPercentage()}  ${randomPercentage()} / ${randomPercentage()}  ${randomPercentage()}`,
        background: randomColor(),
        transitionDuration: `${FOOTER_INTERVAL_MS / insaneFactor}ms`,
      };
    },
    randomTextStyle() {
      const { insaneFactor } = this;
      this.textStyle = {
        transform: `rotate(${randomNumber(
          -3 * insaneFactor,
          3 * insaneFactor,
        )}deg) scale(${randomNumber(0.7 * insaneFactor, 1.3 * insaneFactor)})`,
        color: randomColor(),
        transitionDuration: `${TEXT_INTERVAL_MS / insaneFactor}ms`,
      };
    },
  },
};
</script>

<style scoped>
footer {
  margin-top: 1rem;
  padding: 3rem 0;
  transition-property: border-radius, background;
  text-align: center;
}
footer .text {
  transition-property: color, transform;
}
.insane-mode-label {
  display: block;
  margin-top: 2rem;
}
</style> 
```

## 为你挑战！

我希望你觉得这篇文章有趣。如果你想了解更多关于不同类型的无渲染组件，请观看我的视频,里面有更多的实时编码例子。

Footer.vue 中的`mounted`钩子中还有两行。你能想出一个方法来扩展`<Interval>`以便我们可以消除整个`mounted`钩子吗？偷看[公关](%5Bhttps://github.com/ycmjason-talks/2019-06-20-vue-js-london-meetup-11/pull/2%5D(https://github.com/ycmjason-talks/2019-06-20-vue-js-london-meetup-11/pull/2))在这里得到的想法。

```
 mounted() {
    this.randomFooterStyle();
    this.randomTextStyle();
  }, 
```

## 为什么？

我们在[开发了非常酷的产品，并用 Vue 验证了](https://www.askattest.com/)。我们发现这种模式在很多方面都是有益的，例如可维护性、正确性、可测试性等。如果您想加入这个才华横溢的团队，[今天就在这里申请](https://jobs.lever.co/attest/d8c2fa28-5fbd-4e6a-b759-bf1f1e07cfde)！

另外，我们喜欢基于函数的 RFC。
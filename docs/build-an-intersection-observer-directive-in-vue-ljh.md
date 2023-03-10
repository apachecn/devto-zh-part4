# 在 Vue 中构建一个交叉点观察器指令

> 原文：<https://dev.to/alexsasharegan/build-an-intersection-observer-directive-in-vue-ljh>

在这篇文章中，我想分享我将`IntersectionObserver` API 集成到 Vue 应用程序中的经验。最后，我们将有一个定制的指令，抽象出动态注册和注销 DOM 元素。

# 路口观察者

当您需要跟踪进入视图的元素时，查看文档滚动并计算元素偏移量曾经是唯一的方法。数学并不特别复杂，但是知道使用哪些布局属性以及如何计算相对于正确元素的位置是一项痛苦的任务。此外，由于`scroll`非常快速地触发大量事件，如果您的计算和后续处理超出了帧预算，很容易导致 jank 很可能是因为在一个帧中处理了太多的事件。

输入`IntersectionObserver`。顾名思义，`IntersectionObserver`的实例可以观察许多元素，并在元素与 viewport 或另一个元素*(通常是一些可滚动的容器)*相交或停止相交时调用回调。内置的类能够有效地计算交集，并且它使用简单得多的代码*(没有数学！)*。在这个不错的抽象之上，`IntersectionObserver`还处理经常被遗忘的场景*(比如`resize`事件)*以及特别困难的场景*(比如`<iframe>`元素)*。

在我们开始将这个 API 集成到 Vue 中之前，这里有一些资源可以提供关于 Vue 指令和`IntersectionObserver`的更多背景信息:

*   检视自订指南
*   [交叉观测者进入苏尔马视野](https://developers.google.com/web/updates/2016/04/intersectionobserver)
*   [MDN 交叉点观察器 API 概述](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)
*   [MDN intersect observer 参考](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserver)

# 入门

在 Vue 中使用`IntersectionObserver`的第一个挑战是组件的 DOM 是模板和状态的产物。声明性的组件 UI 旨在让我们远离 DOM，但是使用我们的观察者需要将它插入到我们的真实元素中，而不是我们的模板中。这意味着我们必须亲自动手，深入研究组件的原始元素，并对组件的生命周期保持警惕。

## 又快又脏

首先:让我们只是原型的东西，并使其工作。我将从一个 codesandbox vue 项目开始，用一个大的项目列表替换`App.vue`组件以溢出视口。通过一些可滚动的虚拟内容，我们可以检测一个项目何时进入/退出视图。

### 做一个大单子

让我们从制作溢出列表开始。为了创建一个虚拟元素列表，我们将使用一个名为`range`的计算属性。该属性不使用组件实例中的任何字段，因此它实际上是一个常量。创建一个类似范围的数字数组`1-100`的最短方法是使用一个基于 iterables 的技巧。

```
Vue.extend({
  computed: {
    range() {
      return Array.from({ length: 100 }, (_, i) => i + 1);
    },
  },
}); 
```

`Array.from`接受任何 iterable 作为它的第一个参数，然后接受一个可选的映射函数来转换 iterable 产生的每一项。在这个感觉像是完全欺骗的过程中，我们通过简单地创建一个具有数字长度属性的对象来创建一个 100 项的 iterable:`{ length: 100 }`。我们的转换跳过了从 iterable *(因为它们是空的)*产生的值，而是返回索引加 1。你可以想象一下`Array.from`的内部，启动一个老式的 for 循环，并在每次迭代中调用我们的 transform 函数:

```
// The default transform just returns whatever is yielded from the iterable.
const identity = x => x;

const Array = {
  from(iterable, transform = identity) {
    let list = [];
    for (let i = 0; i < iterable.length; i++) {
      list.push(transform(iterable[i], i));
    }
    return list;
  },
}; 
```

为了呈现列表，我们可以使用一个`v-for`指令。我们将放置一个引用我们的 id 的数据属性，以便稍后我们可以从交叉点观察器的回调中引用元素。我们还将在这里放置一个 ref，这样我们可以将这些元素传递给我们的观察者进行观察。在带有`v-for`的元素上放置一个 ref 将会给我们一个位于`vm.$refs.items`的元素数组。

```
<template>
  <ul class="list">
    <li ref="items" v-for="i in range" :key="i" class="item" :data-id="i">
      Item Number #{{i}}
    </li>
  </ul>
</template> 
```

### 管理状态

现在我们需要弄清楚如何存储视图中的项目。我们可以用视图中的 id 填充一个数组，但是当对观察者的变化做出反应时，我们必须过滤列表中每个不相交的条目，并推送每个相交的条目。这使得添加很便宜，但删除可能很昂贵。

为了提高数组的性能，我们可以使用一组。`Set#has`、`Set#add`和`Set#delete`方法将使移除离开视图的项目和添加进入视图的项目变得快速和容易。set 的问题是 Vue 2.x 无法观察到它的变化。我们必须等待 Vue 3.x 利用`Set`和其他更新的内置。

我们可以使用一个对象来存储哪些 id 在视图中，方法是使用 id 作为键，使用布尔值- `true`表示它在视图中，`false`或没有键表示不在视图中。这使得添加项目就像添加一个值为`true`的新属性一样简单，删除项目可以从对象中排除，或者简单地切换到`false`。这有一个警告:Vue 不能观察到对新的或删除的属性的更改。我们必须小心使用`Vue.set`或者用一个新的替换我们的物体，这样 Vue 将触发它的反应系统来观察新物体的额外属性。

```
Vue.extend({
  data() {
    return {
      // Record<string, boolean>
      inViewById: {},
    };
  },
}); 
```

除了反应性警告之外，我们还需要考虑这样一个事实，即当用作对象键时，我们的数字 id 将被转换为字符串。这将只是一个股票显示的项目目前在视图中。我们希望对条目进行排序，这样我们就不会看到一堆混乱的条目 id。

```
Vue.extend({
  computed: {
    inView() {
      return Object.entries(this.inViewById)
        .filter(this.isInView)
        .map(this.pluckId)
        .sort(this.sortAtoi);
    },
  },
  methods: {
    // Destructure the Object Entry of key, value (dropping the key)
    isInView([, inView]) {
      return inView;
    },
    pluckId([i]) {
      return i;
    },
    // Sort ascii to int (a to i) is a sort function
    // that properly sorts numbers when passed as strings.
    sortAtoi(a, b) {
      return Number(a) - Number(b);
    },
  },
}); 
```

### 创建观察者

最后我们可以实例化一个`IntersectionObserver`。我们可以在我们的组件`data`中做到这一点，但是我们不需要它是反应式的，我甚至不确定 Vue 可以让观察者的属性有多少是反应式的。我们可以使用`created`生命周期钩子，但是我们的组件 DOM 不可访问。我们将使用`mounted`生命周期挂钩，这样我们就可以轻松获得一切，同时也因为该挂钩不在 <abbr title="Server-Side Rendering">SSR</abbr> 上下文中运行。

我们将实例化`IntersectionObserver`，它接受一个回调来处理它所观察到的元素的变化。我们将把它设置为下一步要创建的方法。我们也可以传递一个 options 对象作为第二个参数，但是现在让我们使用默认值。

创建完观察者后，我们将使用放置在`v-for`上的 ref 遍历元素列表。我们告诉我们的新观察者观察每个元素，然后我们将保存一个观察者的句柄，这样我们可以在组件被破坏之前断开它并释放它的资源。

```
Vue.extend({
  mounted() {
    let observer = new IntersectionObserver(this.handleIntersection);
    for (let el of this.$refs.items) {
      observer.observe(el);
    }
    this.observer = observer;
  },
  beforeDestroy() {
    this.observer.disconnect();
  },
}); 
```

这就是有趣的地方。我们的观察者回调通过一个由`IntersectionObserverEntry`对象组成的[数组和一个对我们的观察者*(已经保存在我们的组件实例上)*的引用来调用。我们观察到的每个元素都有一个条目——所以列表中的每个元素。我们可以遍历这个列表，并使用条目的`isIntersecting`属性来确定它是否在视图中。](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserverEntry)

有趣的部分是管理我们的状态，因为如果我们想在视图中添加或删除属性，我们必须给 Vue 新的对象。这里我们已经创建了一个方法来克隆我们的地图，但是只有当项目在视图中时才添加到地图中。我们可以通过这种方式保持对象较小，这有利于我们的克隆过程以及视图中 id 的排序列表。

一旦我们有了视图中的新地图，我们就可以迭代条目，并将可见性与我们的状态同步。如果一个项目是交叉的，我们将该 id 设置为`true`。如果不相交，我们需要检查在旧地图中是否可见，并设置为`false`。这些将是离开视图的项目。通过在`true`时只设置它为`false`，我们可以继续保持最小尺寸的地图。

最后要做的事情是在我们的组件实例上分配新地图。这将触发 Vue 观察新对象，检测变化并重新渲染。

```
Vue.extend({
  methods: {
    handleIntersection(entries, observer) {
      let inViewById = this.cloneInViewById();

      for (let entry of entries) {
        let id = entry.target.dataset.id;
        if (entry.isIntersecting) {
          // You could check if this was not already true
          // to determine the item just came into view.
          inViewById[id] = entry.isIntersecting;
        } else if (inViewById[id]) {
          // Leaving view.
          inViewById[id] = false;
        }
      }

      this.inViewById = inViewById;
    },
    cloneInViewById() {
      let inViewById = {};
      for (let [id, inView] of Object.entries(this.inViewById)) {
        if (inView) {
          inViewById[id] = true;
        }
      }
      return inViewById;
    },
  },
}); 
```

## 快速而肮脏的结果

现在来看看运行中的代码！我已经用我们的代码片段构建了 codesandbox。我们的组件正确地跟踪哪些项目在屏幕上可见，并更新我们的 ticker。这意味着我们正确地设置了观察器，并以 Vue 2.x 友好的方式管理我们的状态。

[https://codesandbox.io/embed/lo7yz](https://codesandbox.io/embed/lo7yz)

### 问题

现在我们有了一个可行的实现，我们还缺少什么呢？

我们的例子展示了元素的静态列表，但是如果我们有一个
动态列表会发生什么呢？可以通过用户交互来添加或删除项目，但是我们的观察者仍然会看到原始的项目集。如果我们在加载组件时呈现一个空列表，然后从数据获取中获得一个长列表，会发生什么？我们的观察者将无所事事，什么也观察不到。

如果我们想使用一个从父组件作为道具传递过来的观察者，该怎么办？我们需要对观察者的变化做出反应。我们可能还需要做好准备，不要一开始就给出一个观察者，或者观察者在组件的生命周期中消失。

# 遵守指令

我们需要的是一种方法，当在组件的 DOM 中添加和删除元素时，可以挂钩到较低层次的 Vue 机制。谢天谢地，有一种方法可以做到这一点，这是一个一流的 Vue API:自定义指令。

## 重构为指令

现在我们需要看看我们应该从我们的原型中提取什么，并放入指令中。我们的指令对观察者没有任何控制，除了它将作为一个指令道具给出。我们将涵盖元素插入、更新和指令解除绑定的用例。使用该指令只需一行代码就可以将我们的观察者传递给我们的指令。这里是我们的大列表的上下文:

```
<template>
  <ul class="list">
    <li
      v-observe="observer"
      ref="items"
      v-for="i in range"
      :key="i"
      class="item"
      :data-id="i"
    >
      Item Number #{{i}}
    </li>
  </ul>
</template> 
```

#### 插入

当插入一个元素时，如果给了我们一个观察者，就向观察者注册这个元素。

#### 更新:未观察到

如果给了我们一个观察者，就用 observer 注册这个元素。

#### 更新:已经观察到

如果给我们一个观察者，检查它是否是同一个观察者。如果不同，尝试注销旧的观察器，并注册新的观察器。如果是同一个观察者，什么也别做。

如果我们没有观察员，尝试注销旧的观察员。

#### 指令解除绑定

如果我们被观察，尝试注销旧的观察者。

## 实现

正如您所看到的，有大量的用例需要支持无缝抽象。在列出需求之后，我可以看到我们将需要缓存两个状态:观察者和我们当前是否被观察。我们可以使用观察者的存在来推断我们是否被观察，但是我发现添加一个数据属性可以更容易地查看事情是否正常。

为了跟踪状态，我们将直接在元素上缓存观察者。为了确保我们不会与现在和将来的任何 DOM 属性发生冲突，我们可以创建一个局部符号，让我们可以独占访问缓存的观察者。我们将通过使用 camelcase: `element.dataset.vObserved = "yes|no"` *(将管道字符读作“or”)*中元素的数据集，使数据属性作为`data-v-observed="yes|no"`出现在 DOM 中。

接下来是一个完整的指令实现，看起来太单调乏味了，无法一行一行地介绍。`insert`和`unbind`的案例相对容易理解，但是`update`却很棘手。我已经尽了最大努力，通过利用早期回报和使用希望使事情更具可读性的名称来降低许多可能情况的复杂性。

```
const yes = "yes";
const no = "no";
const kObserver = Symbol("v-observe");

function markObserved(el) {
  el.dataset.vObserved = yes;
}
function markNotObserved(el) {
  el.dataset.vObserved = no;
}
function cacheObserver(el, observer) {
  el[kObserver] = observer;
}
function removeCachedObserver(el) {
  el[kObserver] = undefined;
}

export default {
  inserted(el, { value: observer }) {
    if (observer instanceof IntersectionObserver) {
      observer.observe(el);
      markObserved(el);
      cacheObserver(el, observer);
    } else {
      markNotObserved(el);
      removeCachedObserver(el);
    }
  },

  update(el, { value: observer }) {
    let cached = el[kObserver];
    let sameObserver = observer === cached;
    let observed = el.dataset.vObserved === yes;
    let givenObserver = observer instanceof IntersectionObserver;

    if (!observed) {
      if (givenObserver) {
        observer.observe(el);
        markObserved(el);
        cacheObserver(el, observer);
      }

      return;
    }

    if (!givenObserver) {
      markNotObserved(el);
      if (cached) {
        cached.unobserve(el);
        removeCachedObserver(el);
      }
      return;
    }

    if (sameObserver) {
      return;
    }

    if (cached) {
      cached.unobserve(el);
    }

    observer.observe(el);
    markObserved(el);
    cacheObserver(el, observer);
  },

  unbind(el) {
    let cached = el[kObserver];
    if (cached instanceof IntersectionObserver) {
      cached.unobserve(el);
    }
    markNotObserved(el);
    removeCachedObserver(el);
  },
}; 
```

## 最终结果

现在你有了它——我们的原型被转换成使用我们定制的`v-observe`指令！她仍然像以前一样工作，但现在您应该能够热交换列表中的项目，以及更改交叉点观察点。

[https://codesandbox.io/embed/5zq4p](https://codesandbox.io/embed/5zq4p)
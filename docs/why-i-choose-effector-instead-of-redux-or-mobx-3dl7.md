# 为什么我选择 Effector 而不是 Redux 或者 MobX？

> 原文：<https://dev.to/brainhub/why-i-choose-effector-instead-of-redux-or-mobx-3dl7>

[效应器](https://effector.now.sh/)是一个全新的反应式状态管理器。其雄心勃勃的团队旨在解决现有解决方案存在的所有问题。从头开始编写库的核心花了六个月的时间，最近该团队发布了第一个[稳定版本](https://github.com/zerobias/effector/releases/tag/effector%4019.0.0)。

在本文中，我将展示为什么我更喜欢在我的新项目中使用 Effector 而不是其他状态管理器。让我们从效应器 API 开始。

## 基础知识

Effector 使用了两个您可能已经熟悉的概念:*存储*和*事件*。

一个*商店*是一个保存一些值的对象。我们可以用`createStore`助手:
创建商店

```
import {createStore} from 'effector'

const counter = createStore(0) // create store with zero as default value

counter.watch(console.log) // watch store changes 
```

Enter fullscreen mode Exit fullscreen mode

商店是轻量级的，所以当你需要在应用程序中引入一些状态时，你只需要创建一个新的商店。

那么我们如何更新我们的商店呢？事件！你用`createEvent`助手创建事件，并通过对事件做出*反应来更新你的商店:* 

```
import {createStore, createEvent} from 'effector'

const increment = createEvent('increment')
const decrement = createEvent('decrement')
const resetCounter = createEvent('reset counter')

const counter = createStore(0)
  .on(increment, state => state + 1) // subscribe to the event and return new store value
  .on(decrement, state => state - 1)  
  .reset(resetCounter)

counter.watch(console.log) 
```

Enter fullscreen mode Exit fullscreen mode

事件就像 Redux 术语中的“动作”，`store.on(trigger, handler)`有点像`createStore(reducer)`。事件只是可以从代码中的任何地方调用的函数。

效应器实现了反应式编程范例。事件和存储被认为是反应性实体(换句话说，*流*，它们有一个`watch`方法，允许订阅事件和存储更改。

### 与 React 融合

一个组件可以通过从`effector-react`包中调用`useStore`钩子来连接到商店。效应器事件可以作为事件处理程序传递给 React 子元素(`onClick`等)。)

```
import React from 'react'
import ReactDOM from 'react-dom'
import {createStore, createEvent} from 'effector'
import {useStore} from 'effector-react'

const increment = createEvent('increment')
const decrement = createEvent('decrement')
const resetCounter = createEvent('reset counter')

const counter = createStore(0)
  .on(increment, state => state + 1)
  .on(decrement, state => state - 1)
  .reset(resetCounter)

counter.watch(console.log)

const Counter = () => {
  const value = useStore(counter) // subscribe to store changes

  return (
    <>
      <div>Count: {value}</div>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={resetCounter}>reset</button>
    </>
  )
}

const App = () => <Counter />
const div = document.createElement('div')
document.body.appendChild(div)
ReactDOM.render(
  <App/>,
  div
) 
```

Enter fullscreen mode Exit fullscreen mode

### 与其他框架集成

### 视图

有 [effector-vue](https://effector.now.sh/en/api/effector-vue/effector-vue) 包。

### 身材苗条

效果器商店是*可观察的*，所以你不需要任何额外的包来使用它们。只需在模板中的商店名称前加上`$`:

```
// Counter.svelte
<script context="module">
  import effector from 'effector/effector.umd.js';

  export const increment = createEvent('increment')
  export const decrement = createEvent('decrement')
  export const resetCounter = createEvent('reset counter')
  export const counter = effector.createStore(0)
    .on(increment, (n) => n + 1)
    .on(decrement, state => state - 1)
    .reset(resetCounter)
</script> 
// App.svelte
<script>
  import { counter, increment, decrement, resetCounter } from './Counter.svelte'
</script> 
<div>Count: {$counter}</div> <button on:click={increment}>+</button> <button on:click={decrement}>-</button> <button on:click={resetCounter}>reset</button> 
```

Enter fullscreen mode Exit fullscreen mode

## 副作用

有了效应器，你不需要 thunks 或 sagas 来处理副作用。Effector 有一个方便的助手叫做`createEffect`，它包装了一个异步函数并创建了三个你的存储可以订阅的事件:一个初始化器(效果本身)和两个解析器叫做`done`和`fail`。

```
const getUser = createEffect('get user');
getUser.use(params => {
  return fetch(`https://example.com/get-user/${params.id}`)
    .then(res => res.json())
})

// OR

const getUser = createEffect('get user', {
  handler: params => fetch(`https://example.com/get-user/${params.id}`)
    .then(res => res.json())
})

const users = createStore([]) // <-- Default state
  // getUser.done is the event that fires whenever a promise returned by the effect is resolved
  .on(getUser.done, (state, {result, params}) => [...state, result]) 
```

Enter fullscreen mode Exit fullscreen mode

## 高级用法:`combine`，`map`

Effector 的一个令人惊叹的特性是计算存储。计算存储可以使用存储的`combine`助手或`.map`方法来创建。这允许只订阅对特定组件有*影响*的变更。在 React 应用程序中，性能可能会受到不必要的状态更新的严重影响，因此 Effector 有助于消除它们。

`combine`创建一个新的商店，该商店根据几个现有的商店计算其状态:

```
const balance = createStore(0)
const username = createStore('zerobias')

const greeting = combine(balance, username, (balance, username) => {
  return `Hello, ${username}. Your balance is ${balance}`
})

greeting.watch(data => console.log(data)) // Hello, zerobias. Your balance is 0 
```

Enter fullscreen mode Exit fullscreen mode

`map`允许创建衍生商店:

```
const title = createStore("")
const changed = createEvent()

const length = title.map((title) => title.length)

title.on(changed, (oldTitle, newTitle) => newTitle)

length.watch((length) => console.log("new length is ", length)) // new length is 0

changed("hello") // new length is 5
changed("world")
changed("hello world") // new length is 11 
```

Enter fullscreen mode Exit fullscreen mode

## 与其他国家管理者的比较

### Redux

*   大多数使用 Redux 的项目在单个存储中实现整个应用程序状态。拥有多个商店并不被禁止，但是正确地做到这一点有点棘手。效果器是*制造的*同时与不同商店的*批次*一起工作。
*   Redux 非常明确，但也非常冗长。效应器需要较少的样板代码，但是所有的状态依赖仍然是显式的。
*   Redux 最初是用纯 JS 编写的，没有考虑静态类型。Effector 具有更广泛的现成类型支持，包括大多数助手和方法的类型推断。
*   Redux 有很棒的开发工具。Effector 现在有些滞后，但是团队已经有了开发工具的计划，这些工具可以将您的应用程序可视化地表示为连接的存储和事件的图形。

### MobX

*   缩小并 gzip 后，MobX 差不多 20kb(反应绑定的 [14.9kb](https://bundlephobia.com/result?p=mobx@5.13.0) + [4.6kb](https://bundlephobia.com/result?p=mobx-react@6.1.3) )，而效应器不到 8 kb(反应绑定的 [5.8 kb](https://bundlephobia.com/result?p=effector@20.1.2) + [1.7 kb](https://bundlephobia.com/result?p=effector-react@20.2.2) )。
*   MobX 内部有很多*魔法*:对可观察数据变化的隐式订阅，“可变”状态对象使用代理来分发更新，等等。Effector 使用不可变的状态，显式地组合存储的状态，并且只允许通过事件来改变它。
*   MobX 鼓励让您的数据模型靠近视图。使用 Effector，您可以完全隔离数据模型，并保持 UI 组件的 API 简洁明了。
*   可能难以与自定义数据结构一起使用。

### RxJS

*   严格来说，虽然 RxJS 解决了许多任务，但它是一个反应式扩展库，而不是状态管理工具。另一方面，Effector 是专门为管理应用程序状态而设计的，它有一个简单易学的小 API。
*   RxJS 不是“无故障”的。特别是，默认情况下，计算数据的同步流不会产生一致的更新:参见[关于不同的反应式状态管理工具如何处理这项任务的示例](https://codesandbox.io/s/effector-comparison-r9qy2)。

## 我为什么选择效应器

以下是我认为 Effector 优于大多数类似工具的地方:

*   富有表现力和简洁的 API。
*   反应式编程范式的核心。
*   稳定，生产就绪。
*   伟大的表现，我也没有看到任何内存泄漏。
*   有动力的团队，伟大的社区。

## 结论

Effector 不是银弹，但它无疑是对国家管理的一种新尝试。不要害怕尝试新事物，不要害怕偏离最流行的解决方案。感兴趣吗？现在试试[效应器](https://effector.now.sh/)！

## 感谢

*   Andrey Sitnik [@ai](https://github.com/ai) -文章推广
*   亚历山大·克拉德科夫 [@A1992](https://t.me/A1992) -事实核查
*   arty om Arutyunyan[@ artalar](https://github.com/artalar/)-事实核查
*   亚历山大·丘德诺夫 -校对、编辑

## 链接

*   [效果器 GitHub](https://github.com/zerobias/effector)
*   [效果器推特](https://twitter.com/EffectorJS)
*   [效果器文档](https://effector.now.sh/en/introduction/installation)
*   [牛逼-效果器](https://github.com/ilyalesik/awesome-effector) -效果器牛逼列表。
*   [效应器:强大快速的 5kb 状态管理器](https://codeburst.io/effector-state-manager-6ee2e72e8e0b)
*   [效果器——状态管理器你应该试试](https://itnext.io/effector-state-manager-you-should-give-a-try-b46b917e51cc)
*   [你错过的反应式编程入门](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)
*   [反动宣言](https://www.reactivemanifesto.org/)
*   [flaxom](https://github.com/artalar/flaxom)
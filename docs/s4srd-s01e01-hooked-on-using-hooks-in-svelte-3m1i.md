# 第一季第一集

> 原文：<https://dev.to/tiagobnobrega/s4srd-s01e01-hooked-on-using-hooks-in-svelte-3m1i>

我在推特上认识了 dev.to，所以我决定写第一篇关于@sveltejs 的文章。不仅如此，如何*反应*你苗条的应用程序(我相信哈里斯会喜欢的，对不对？).不仅如此，这是一个系列。

**现在请忍耐一下**暂时不要冲动地关闭浏览器窗口，欢迎来到:

# 

<center>🙃</center>

# 身材苗条，适合固执己见的开发者

#### 摘要

行...玩笑归玩笑，这里的想法是展示 React 开发人员熟悉的一些概念是如何被实现的。绝不是*谁做得更好*(无评判区)的较量。此外，所展示的代码没有经过战场测试，没有经过压力测试，也不是军用级别的技术。这只是我到目前为止发现的实现事情的方法，请随意对任何事情发表评论。

#### TL；速度三角形定位法(dead reckoning)

苗条的生命周期函数可以从任何地方被调用，并被隔离在隔离的模块中。然后可以导入它，供任意多个组件使用。它们必须在组件初始化期间被调用，但是你不必担心排序和条件调用。

如果你有兴趣弄清楚如何用一个苗条的钩子来使用状态或上下文，请继续关注下一集。

#### 钩子到底是什么，它是做什么用的？

借用 **[@dan_abramov](https://dev.to/dan_abramov)** 此处:

> 如果不重组代码或引入 Observables 这样的抽象，就无法从类组件中提取“观察窗口大小并更新状态”或“随时间动画化值”这样的行为。这两种方法都伤害了我们喜欢的 React 的简单性。钩子正好解决了这个问题。钩子允许你通过一个函数调用来使用函数的反应特性(比如状态)。React 提供了一些内置的挂钩，公开了 React 的“构建块”:状态、生命周期和上下文。>

所以一个钩子本质上是一个**函数**到**提取行为**，允许你**对生命周期**做出反应**访问状态和上下文**。

#### 如何做到身材苗条？

**提取行为**是所有函数都要做的，这是最简单的部分。✔️Done.但是对生命周期的反应呢？

Svelte 为此公开了几个助手函数: *OnMount，beforeUpdate，afterUpdate，onDestroy，tick* 。它们的工作方式完全一样(除了 tick——那个令人毛骨悚然的小家伙)。因此，为了简单起见，让我们坚持使用 onMount & onDestroy。

现在，假设我们希望在组件被安装或破坏时记录一些东西。首先，让我们针对单个组件:

```
<!-- componentA.svelte -->
<script>
import { onMount, onDestroy } from 'svelte';

onMount(()=>console.log('A component was mounted'))
onDestroy(()=>console.log('A component was destroyed'))
</script>

<h1>Hi, I'm component componentA.svelte</h1> 
```

这很简单，但它可以变得更好。(这是我花了一段时间才找到的，尽管它在文档中)。来自苗条的医生:

> 如果从 onMount 返回一个函数，当组件被卸载时，它将被调用。>

并重构了上面的代码:

```
<!-- componentA.svelte -->
<script>
import { onMount } from 'svelte';

onMount(()=>{
    console.log('A component was mounted')
    return ()=>console.log('A component was destroyed')//⬅️ onDestroy
})
</script>

<h1>Hi, I'm component componentA.svelte</h1> 
```

现在，如果我们想将这个**行为**复制到另一个组件，为了保持代码干燥，我们需要将它提取到一个可重用的模块，比如(你猜对了)一个**函数**。但是一个隔离的函数如何使用组件的 onMount 和 onDestroy 呢？或者换句话说，如何将 onMount 和 onDestroy 注入到一个隔离的函数中？

我的第一个想法是将它作为参数传递。但是事实证明你不需要这么做。onMount 和 onDestroy 只是函数。你可以从任何地方给他们打电话。所以我们的**行为功能模块**可以这样实现:

```
//useLogger.js
import { onMount } from 'svelte';
export default function useLogger(){
    onMount(()=>{
        console.log('A component was mounted')
        return ()=>console.log('A component was destroyed')//⬅️ onDestroy
    })
} 
```

并且用在我们所有的组件中，就像这样:

```
<!-- componentA.svelte -->
<script>
import useLogger from './useLogger';
useLogger();
</script>
<h1>Hi, I'm component componentA.svelte</h1>

<!-- componentB.svelte -->
<script>
import useLogger from './useLogger';
useLogger();
</script>
<h1>Hi, I'm component componentB.svelte</h1> 
```

如果我们想让记录的消息更个性化一点:

```
//useLogger.js
import { onMount } from 'svelte';
export default function useLogger(componentName='A component'){
    onMount(()=>{
        console.log(`${componentName} was mounted`)
        return ()=>console.log(`${componentName} was destroyed`)//⬅️ onDestroy
    })
} 
```

```
<!-- componentA.svelte -->
<script>
import useLogger from './useLogger';
useLogger('Component A');
</script>
<h1>Hi, I'm component componentA.svelte</h1>

<!-- componentB.svelte -->
<script>
import useLogger from './useLogger';
useLogger('Component B');
</script>
<h1>Hi, I'm component componentB.svelte</h1> 
```

现在它看起来多汁！注意一点:*“必须在组件初始化时调用”*。所以你不能在点击处理程序上使用它。好的一面是你不必担心你的钩子的排序或条件调用。

就是这样！我们刚刚实现了一个**函数**来**提取行为**，允许你**对生命周期**做出反应。但是“**访问状态和上下文**部分呢？这是第一季第二集的话题。敬请期待！

扰流板警报

我听到一些传言说 S02 是关于 HOC 的

扰流板警报
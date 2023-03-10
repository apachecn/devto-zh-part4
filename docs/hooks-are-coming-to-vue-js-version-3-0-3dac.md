# 钩子来了 vue . js 3.0 版

> 原文：<https://dev.to/bnevilleoneill/hooks-are-coming-to-vue-js-version-3-0-3dac>

**由[nwo se lotana](https://blog.logrocket.com/author/nwoselotanna/)撰写**

本文介绍了一个名为 Hooks 的实验性 Vue 特性。

## 开始前

这篇文章适合所有阶段的开发者，包括初学者。在阅读本文之前，这里有一些你应该已经具备的东西。

您需要在您的电脑中安装以下设备:

*   [Node.js 版本 10.x](https://nodejs.org/en/) 及以上安装。您可以通过在终端/命令提示符下运行以下命令来验证您是否知道:

```
node -v 
```

Enter fullscreen mode Exit fullscreen mode

*   代码编辑器:强烈推荐使用 Visual Studio 代码
*   [Vue 的最新版本](https://vuejs.org/)，全球安装在您的机器上
*   安装在您机器上的 Vue CLI 3.0 。为此，请先卸载旧版本的 CLI:

```
npm uninstall -g vue-cli 
```

Enter fullscreen mode Exit fullscreen mode

然后安装新的:

```
npm install -g @vue/cli 
```

Enter fullscreen mode Exit fullscreen mode

*   在这里下载一个 Vue starter 项目
*   解压缩下载的项目
*   导航到解压缩后的文件，并运行命令以保持所有依赖项最新:

```
npm install 
```

Enter fullscreen mode Exit fullscreen mode

## 简介:钩子

最初，包含状态逻辑的 React 组件必须是一个类组件。尽管 React 中已经有了无状态的功能组件，但是仍然需要创建适应状态逻辑的组件，而不是类。那是胡克斯出生的时候。钩子是在功能组件中使用状态逻辑的一种方式，因此不需要编写类。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## 上课怎么了？

首先，类没有错，但是 React 团队发现[理解类如何工作已经成为 React 采用的一个障碍](https://reactjs.org/docs/hooks-intro.html#classes-confuse-both-people-and-machines)。随着项目规模和复杂性的增加，它可能很难理解，也可能变得模糊不清。

### 但是我通常不在 Vue JS 中使用类..

如果你是一个 Vue 开发者，你可能想知道为什么讨论类，因为你在你的 Vue 项目中默认不使用类。虽然这是真的，但是 Vue JS 允许您使用带有 [mixins](https://vuejs.org/v2/guide/mixins.html) 的无状态功能组件。使用 Vue mixins，您可以在特定文件中定义逻辑或功能，并在功能组件中使用甚至重用它。

## mixins 的问题

在几个月前的一篇博客文章中， [Sarah Drasner](https://twitter.com/sarah_edo?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor) ，一位非常受欢迎的 Vue 核心团队成员写了她与 Vue JS 的创造者[尤雨溪](https://twitter.com/youyuxi?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor)的对话。Sarah 揭示了一个 mixin 不能消费或使用来自另一个 mixin 的状态，这使得封装逻辑的链接很难实现。这就是 Vue Hooks 解决的混音限制。

## 引入 Vue 挂钩

Vue 钩子基本上是 mixin 的增强版本，如果你不知道 mixin 是什么，它们是 Vue 中的一个平台，用于重用组件之间的逻辑(在这篇文章中你会看到 mixin 的快速演示)。Vue Hooks 允许你将逻辑从一个钩子传递到另一个钩子，你也可以从另一个钩子使用一个钩子中的状态。所以就像在 React 中一样，Vue 中的钩子是在函数中定义的，这可以是一种更干净、更灵活的方式来定义和共享逻辑，并且可以返回状态。

## 演示 A: Vue mixins

如果你从一开始就关注这篇文章，你一定已经下载了[起始项目文件](https://github.com/viclotana/vue-canvas)，并在你的 VS 代码应用程序中打开了它。我们将创建一个包含计数器逻辑的 mixin，然后将其导入到任何选择的组件中。首先，在根目录下创建一个名为 mixins 的文件夹并创建一个新文件，命名为`clickMixin.js`并将下面的代码块复制到其中:

```
export default { 
    data (){
      return{
        count: 1,
        double: 2
      }
    },
    methods: {
      clicked(){
       this.count++; 
       this.double = this.count*2;
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

这个 mixin 包含计数器逻辑，还包含一个返回双倍计数的变量，您会看到 export 语句，因为它必须被导入到您选择的组件中。打开您的`Test.vue`组件，并将下面的代码块复制到其中:

```
<template>
  <div> 
    <button v-on:click="clicked()">Button 1</button>
    <h2>{{this.count}}</h2>
    <h2>{{this.double}}</h2>
  </div>
</template>
<script>
import clickMixin from '../Mixins/clickMixin'
export default {
  name: 'Test',
  mixins: [clickMixin]
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这里你可以看到 mixins 是如何被导入和注册的，它是一个 Vue 实例属性，就像数据、方法或计算属性一样。您还可以看到，在模板内部，您可以在 JavaScript 中访问它，因为它与 mixin 相关(几乎就像 mixin 是在组件内部定义的)。如果您在开发服务器上运行该应用程序，它应该是这样的:

[![application with mixin included](img/62ffc2ff90ee1afb6b252606a6be58e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DM2dFC5e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blog.logrocket.com/wp-content/uploads/2019/08/vuebutton1and2.png%3Fresize%3D1424%252C548%26ssl%3D1)

当您单击按钮 1 时，计数器增加 1，下面的数字是计数器数字的两倍，正如您的代码中的模板所示。

## 演示 b:视图挂钩

你可以很容易地用 Vue 钩子重新创建这个逻辑，钩子的目的是在将来潜在地替换 mixins。首先，你要用节点包管理器安装 vue-Hooks 包。在 VS 代码中打开一个新的终端并运行:

```
npm install vue-Hooks 
```

Enter fullscreen mode Exit fullscreen mode

然后打开您的`main.js`文件，在新的 Vue 语句前用一行命令行初始化钩子:

```
Vue.use(Hooks); 
```

Enter fullscreen mode Exit fullscreen mode

打开 components 文件夹并在其中创建一个新文件，命名为`Modal.vue`，然后导航回根目录并创建一个名为 Hooks 的新文件夹。在 Hooks 文件夹中创建一个名为`Hooks.js`的新文件，并将下面的代码块复制到其中:

```
import { useData, useMounted, useEffect, useComputed, useUpdated} from 'vue-Hooks'
export default function clickedHook(){
const data = useData({ count:1 })
const double = useComputed(() => data.count * 2)
useMounted(()=> {console.log('mounted')});
useUpdated(()=> {console.log('updated')});
useEffect(()=> {
     console.log('DOM re-renders....')
 });
return {
     data, double
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

就像在 React 中一样，Vue Hooks 借用了 use-prefix 语法，并以 Vue 的方式使用它。您还会注意到，每个 Vue 实例可用的生命周期挂钩都可以在 Vue 挂钩内访问，其中一些是:

*   `useData`:处理钩子内部的数据初始化，因此计数在钩子内部被初始化
*   这更像是钩子内部的计算属性，所以双重计算是在钩子内部完成的
*   除了钩子之外，其行为与 Vue 实例中挂载的生命周期钩子完全一样
*   与 Vue 实例中更新的生命周期挂钩的行为完全一样，除了挂钩
*   `useEffect`:处理 DOM 重新渲染的逻辑

你还可以导入其他属性，整个列表可以在 GitHub 的找到。您会注意到它被导出为一个函数，打开您之前创建的`modal.vue`文件，并将下面的代码块复制到其中:

```
<template>
  <div> 
    <button v-on:click="data.count++">Button 2</button>
    <h2>{{data.count}}</h2>
    <h2>{{double}}</h2>
  </div>
</template>
<script>
import clickedHook from '../Hooks/Hooks'
export default {
  name: 'Modal',
  Hooks(){
    return clickedHook();
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在导入钩子文件后，您可以访问之前在这个组件的钩子中定义的数据和 double 常量。我们还看到，钩子注册与数据注册具有相同的语法，其中设置了函数和返回对象。

### T2】

值得注意的是…

*   你可以直接使用 Vue 挂钩(你的 Vue 版本必须是 2.x)
*   有猜测称 Vue 3.0 版本将正式搭载 Vue 挂钩
*   这是实验性的，所以还不能用于生产
*   点击这里阅读 Sarah Drasner 的博客文章,其中包含了更多的例子

你可以使用 npm 或者通过链接访问 [GitHub 来获得项目资源库。](https://github.com/yyx990803/vue-hooks)

## 结论

这是对 Vue JS 中钩子的一个快速概述，以及它们与启发它的 React 钩子有什么不同。我们还为没有接触过 mixinss 概念的读者强调了 mixin，我们看了一个使用钩子的例子。你对 Vue Hooks 感到兴奋吗？

* * *

**编者按:**看到这个帖子有问题？你可以在这里找到正确的版本。

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[Hooks is coming to vue . js version 3.0](https://blog.logrocket.com/hooks-are-coming-to-vue/)最先出现在 [LogRocket 博客](https://blog.logrocket.com)上。
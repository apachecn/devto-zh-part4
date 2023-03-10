# 使用 Vue JS 中的渲染函数访问虚拟 DOM

> 原文：<https://dev.to/bnevilleoneill/accessing-the-virtual-dom-using-render-functions-in-vue-js-5fh5>

**由[nwo se lotana](https://blog.logrocket.com/author/nwoselotanna/)撰写**

在这篇文章中，我们将通过在 Vue 中使用模板来看看如何使用渲染函数来释放 JavaScript 的力量。

## 开始前

这篇文章适合所有阶段的开发者，包括初学者。在阅读本文之前，这里有一些你应该已经具备的东西。

您需要在您的电脑上安装以下设备:

*   [Node.js 版本 10.x](https://nodejs.org/) 及以上安装。您可以通过在终端/命令提示符下运行以下命令来验证您是否知道:

```
node -v 
```

*   代码编辑器:强烈推荐使用 Visual Studio 代码
*   [Vue 的最新版本](https://vuejs.org/)，全球安装在您的机器上
*   安装在您机器上的 Vue CLI 3.0 。为此，请先卸载旧版本的 CLI:

```
npm uninstall -g vue-cli 
```

然后安装新的:

```
npm install -g @vue/cli 
```

*   在这里下载一个 Vue starter 项目
*   解压缩下载的项目
*   导航到解压缩后的文件，并运行命令以保持所有依赖项最新:

```
npm install 
```

## 简介:Vue 如何在浏览器中工作

Vue JS 关注于 JavaScript 项目的视图层，这就是为什么提供模板来显示你的演示(标记语言)。节点是 DOM 中最小的构造单元，所以当像这样的模板代码被发送到 DOM 时，浏览器会将其分解为节点:

```
<div> 
 <h1>My first heading is this </h1>
 <p>The paragraph text stays here </p>
</div> 
```

因此，标题是一个节点，标题文本是标题节点内的子节点，就像段落是一个节点，其中的文本是子节点一样。为了更新这些节点，Vue 在组装节点时提供了一个模板来编写表示代码。

所以对于像上面这样的段落，你只需在模板部分写下:

```
<p>The paragraph text stays here </p> 
```

或者使用一个渲染函数来创建它，就像这样:

```
render: function (createElement) {
  return createElement('p', 'The paragraph text stays here')
} 
```

在比较这两种方法时，您可以很快注意到 Vue 的抽象程度。然而，render 函数更接近编译器，因为模板代码仍然需要转录成 JavaScript。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## Vue 如何管理节点

为了监控和正确管理这些节点，Vue JS 构建了一个虚拟 DOM，它在这个虚拟 DOM 中做一些脏活来跟踪节点，以及它们如何从挂载点`onInit`变化到卸载时(在`onDestroy`)成为虚拟节点。虚拟 DOM 只是所有虚拟节点的组件树。

## 渲染功能

这是在 Vue 组件内部使用的 JavaScript 函数，用于执行创建元素之类的任务。这里我们看到上面显示的函数:

```
render: function (createElement) {
      return createElement('p', 'The paragraph text stays here')
    } 
```

您可以看到参数`createElement`用于在 DOM 中创建一个新元素，它在幕后返回一个虚拟节点。虚拟节点在 Vue JS 中被称为 VNodes。

渲染函数的`createElement`最多接受三个参数:

1.  第一个参数称为**呈现元素**，它是 createElement 工作所必需的，它可以是一个字符串，甚至是一个函数。它通常是一个 HTML 标记名、一个组件选项或一个解析为标记名的函数。
2.  第二个参数是组件的**定义或者最后一个参数中的 HTML 标记名**。它通常是一个对象，并且是一个可选参数。它是一个数据对象，对应于将在 DOM 中创建的参数。
3.  第三个参数是**的子参数**，它可以是第一个参数的数据值或子节点的字符串或数组。

## 演示

为了说明渲染功能，让我们看一个例子，如果你从一开始就阅读这篇文章，你将已经下载了[启动项目](https://github.com/viclotana/vue-canvas)，解压并在你的 VS 代码应用程序中打开它。打开您的`App.vue`文件，并确保它看起来像这样:

```
<template>
  <div id="app">
    <p>{{greetings}} </p>
  </div>
</template>
<script>
import Test from './components/Test.vue'
export default {
  name: 'app',
  components: {
    Test
  },
  data(){
    return {
      greetings: 'The paragraph text stays here'
    }
  }
}
</script>
<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style> 
```

这正是我们在本文开始时的第一个代码样本。要使用渲染函数方法，请记住它是 Vue 中的一个纯 JavaScript 概念，因此打开 main.js 文件，它应该是这样的:

```
import Vue from 'vue'
import App from './App.vue'
Vue.config.productionTip = false
new Vue({
  render: h => h(App),
}).$mount('#app') 
```

这是 Vue 保存默认挂载语句的地方，也是 app.vue 文件成为根组件的原因。将此代码块添加到新的 Vue 代码块之前，紧接着第三行:

```
import Vue from 'vue'
import App from './App.vue'
Vue.config.productionTip = false
Vue.component('Hello', {
   render(createElement){
    return createElement('p', 'The rendered paragraph text stays here');
 }
})
new Vue({
  render: h => h(App),
}).$mount('#app') 
```

这将创建一个名为 Hello 的新 Vue 组件，然后在其中创建一个段落元素。该段落看起来就像您打开了一个新的。vue 文件类似于项目目录中的测试组件。要测试它，返回到`app.vue`文件，在模板中添加 Hello 组件，并观察它出现在浏览器中。

```
<template>
  <div id="app">
    <p>{{greetings}} </p>
    <Hello />
  </div>
</template> 
```

下面是它在浏览器中的样子:

[![](img/86e4846a00bbd06f9759713476c140d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zcliB_Ag--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blog.logrocket.com/wp-content/uploads/2019/08/renderedparagraphvue.png%3Fresize%3D2000%252C274%26ssl%3D1)

## 利用 JavaScript 的力量

您可以利用您的 JavaScript 知识来真正控制虚拟 DOM，因为您已经知道使用 render 函数可以直接控制 Vue 虚拟 DOM。您可以添加一个带有值的数据对象，然后使用它来引用它，如下所示:

```
Vue.component('Hello', {
  render(createElement){
    return createElement('p', this.greetings);
  },
  data(){
    return {
      greetings: 'The paragraph text stays here'
    }
  }
}) 
```

这将使用数据对象中指定的文本显示创建的段落。

## 使用道具

你也可以使用你最喜欢的 Vue JS 构造，比如道具。如果您的`app.vue`模板部分有一个汽车属性，如下所示:

```
<template>
  <div id="app">
    <p>{{greetings}} </p>
    <Hello car=" driving a Ferari"/>
  </div>
</template> 
```

你可以像这样使用`main.js`文件中的道具来引用它:

```
Vue.component('Hello', {
  render(createElement){
    return createElement('p', this.greetings + this.car);
  },
  data(){
    return {
      greetings: 'The paragraph text stays here'
    }
  },
  props: ['car']
}) 
```

下面是它在浏览器中的样子:

[![render createElement](img/98d7b4b6cbccb4e375043952720cb2f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Sw4S8qeE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2019/08/rendercreateelement.png%3Fresize%3D730%252C140%26ssl%3D1)

## 创建嵌套组件

你也可以像这样用 render 函数嵌套元素:

```
Vue.component('Hello', {
      render(createElement){
        return createElement('ul', 
          [createElement('li',[createElement('p', 'I am a nested list item')])]);
      },
      data(){
        return {
          greetings: 'The paragraph text stays here'
        }
      },
      props: ['car']
    }) 
```

下面是它在浏览器中的样子:

[![nested item list ui](img/a118cf1cff3e91fcb3781a2d02650d0e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_vF10Djm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/blog.logrocket.com/wp-content/uploads/2019/08/nestedlistitem.png%3Fresize%3D730%252C156%26ssl%3D1)

这里可以看到 [Vue 实例 API](https://vuejs.org/v2/api/#Instance-Properties) 和渲染函数文档[。](https://vuejs.org/v2/guide/render-function.html)

## 结论

这很好地介绍了如何使用渲染函数访问 Vue 虚拟 DOM。这是与 DOM 交流的一种非常直接的方式，有了 Vue 和 JavaScript 本身的知识，您可以轻松地完全控制 DOM。黑客快乐！

* * *

**编者按:**看到这个帖子有问题？你可以在这里找到正确的版本。

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

使用 Vue JS 中的渲染函数访问虚拟 DOM 的帖子[首先出现在](https://blog.logrocket.com/accessing-the-virtual-dom-using-render-functions-in-vue-js/)[博客](https://blog.logrocket.com)上。
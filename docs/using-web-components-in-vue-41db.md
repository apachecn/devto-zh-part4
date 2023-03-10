# 在 Vue 中使用 Web 组件

> 原文：<https://dev.to/coryrylan/using-web-components-in-vue-41db>

这篇文章是从我的新电子书 [Web Component Essentials](https://webcomponent.dev) 中摘录的章节

VueJS 是一个新的 JavaScript 框架，最近因其简单的 API 和更容易的学习曲线而广受欢迎。在本帖中，我们将学习如何在 Vue 应用程序中使用 web 组件。我们将创建一个 Vue CLI 项目，并向该项目添加一个简单的下拉 web 组件。你可以在 cli.vuejs.org 的[了解更多关于 Vue CLI 的信息。](https://cli.vuejs.org/)

我们的下拉列表是一个简单的 web 组件，我在 npm 包`web-component-essentials`中构建并发布了
。这是我们下拉式 web 组件的一个剪辑。

[![Web Component in VueJS](img/312ad03d97f3cfff3d39daa5d96f0146.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V601nTyo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wo1o637tk5nfmrq74ngl.png)

## 使用 Vue CLI 创建 Vue 项目

在本例中，我们将使用 Vue CLI 工具来生成一个 Vue 项目。Vue CLI 将提供我们开始构建和运行应用程序所需的所有工具。

首先，我们需要通过 NPM 安装 Vue CLI。我们可以通过运行以下命令来安装 Vue CLI:

```
npm install -g @vue/cli 
```

安装完成后，我们可以通过运行以下命令来创建我们的项目:

```
vue create my-app 
```

该命令将创建一个基本的 Vue 项目，并安装任何依赖项。安装完成后，我们可以通过运行以下命令来安装下拉菜单:

```
npm install web-component-essentials --save 
```

这个命令安装我们在前面章节中发布的下拉包。安装后，我们现在可以将下拉菜单导入到我们的 Vue 应用程序中。在`main.js`
中我们可以添加如下:

```
import Vue from 'vue'
import App from './App.vue'
import 'web-component-essentials'

Vue.config.productionTip = false

new Vue({
  render: h => h(App)
}).$mount('#app') 
```

要运行我们的 Vue 应用程序，我们可以运行以下命令:

```
npm run serve 
```

该命令将在`localhost:8080`启动我们的 Vue 应用程序。我们来看看`HelloWorld.vue`组件。Vue 组件使用单一的文件组织方式。例如，Angular 组件有一个 TypeScript、HTML 和 CSS 文件。Vue 组件有一个包含组件所有三个部分的文件。我们将首先从模板开始。

## Vue 中的属性和事件绑定

Web 组件主要通过三种方式进行通信，设置属性(输入)、发出事件(输出)以及接受元素标签和内容槽之间的动态内容。我们示例中的 dropdown 组件使用了所有这三个 API。

```
// HelloWorld.vue
<template>
  <div>
    <h1>VusJS Application using Web Components</h1>

    <p>
      {{show ? 'open' : 'closed'}}
    </p>

    <x-dropdown :title="myTitle" @show="log">
      Hello from Web Component in Vue!
    </x-dropdown>
  </div>
</template> 
```

我们可以看到一个表达式，显示下拉菜单是打开还是关闭，
`{{show ? 'open' : 'closed'}}`。在下拉组件上，我们使用 Vue 的绑定语法。这种绑定语法适用于所有 HTML 元素以及使用 web 组件的自定义元素。这个模板绑定语法类似于 Angular 中的元素绑定语法。

为了绑定到一个属性，我们使用了`:`字符。为了将一个属性绑定到 dropdown title 属性，我们编写了`:title="myTitle"`。在我们的 Vue 组件中，我们有一个`myTitle`属性，它的值被分配给 dropdown 组件的`title`。

为了监听事件，我们使用`@`字符。我们的下拉菜单有一个单一事件`show`。为了听这个事件，我们写`@show="log"`。每当 show 事件发生时，这个事件绑定将调用 Vue 组件上的 log 方法。

接下来，我们来看看 Vue 组件 JavaScript。

```
<script>
export default {
  name: 'HelloWorld',
  data: function () {
    return {
      myTitle: 'project-vue',
      show: false
    }
  },
  methods: {
    log: function (event) {
      console.log(event);
      this.show = event.detail;
    }
  }
}
</script> 
```

Vue 组件定义包含我们想要绑定到 Vue 模板上的数据和方法属性。在我们的例子中，我们有两个数据属性，`myTitle`和`show`。我们有一个方法`log`，我们看到它被绑定到了`@show`事件。

如果一切都连接正确，我们应该在浏览器中看到类似下面的内容:

[![Web Component in VueJS](img/312ad03d97f3cfff3d39daa5d96f0146.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V601nTyo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wo1o637tk5nfmrq74ngl.png)

使用 web 组件允许我们在我们选择的任何
框架之间共享 UI 组件。VueJS 是构建 JavaScript 应用程序的一个很好的选择，并且可以很好地与现成的 web 组件一起工作。

在这里找到完整的工作演示！
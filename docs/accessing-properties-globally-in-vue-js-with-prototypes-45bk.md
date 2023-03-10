# 使用原型在 Vue.js 中全局访问属性

> 原文：<https://dev.to/bnevilleoneill/accessing-properties-globally-in-vue-js-with-prototypes-45bk>

**由[nwo se lotana](https://blog.logrocket.com/author/nwoselotanna/)撰写**

在这篇文章中，将向您介绍使用 Vue.js 中的原型来全局访问属性，而不是在工作流中使用多个导入。

## 开始前…

这篇文章适合所有阶段的开发者，包括初学者。在阅读本教程之前，您应该已经掌握了以下一些东西:

*   已安装 node . js 10 . x 及以上版本。您可以通过在终端/命令提示符下运行以下命令来验证您是否知道:

```
node -v 
```

Enter fullscreen mode Exit fullscreen mode

*   代码编辑器——我强烈推荐 Visual Studio 代码
*   Vue 的最新版本，全球安装在您的机器上
*   安装在您机器上的 Vue CLI 3.0。为此，请先卸载旧版本的 CLI:

```
npm uninstall -g vue-cli 
```

Enter fullscreen mode Exit fullscreen mode

然后，安装新的:

```
npm install -g @vue/cli 
```

Enter fullscreen mode Exit fullscreen mode

*   在这里下载一个 Vue starter 项目
*   解压缩下载的项目
*   导航到解压缩后的文件并运行下面的命令，以保持所有依赖项最新:

```
npm install 
```

Enter fullscreen mode Exit fullscreen mode

## 导入如何帮助我们访问文件

在您的 Vue 项目中工作时，访问另一个项目或嵌套项目中的新组件的最快方法就是导入所需的组件或资源。

然而，这很容易变得低效，因为您将不得不重复 import 语句，甚至为每次导入注册组件(如果它们*是*组件)。幸运的是，Vue 提供了一种更有效的方法来处理这种情况，在这种情况下，您已经全局定义了一个数据对象或一个传入的 HTTP 请求。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## `Vue.prototype`

为了在 Vue 应用程序中创建一个全局属性，然后通过属性语句而不是导入语句来访问它，Vue 提供了一个我们称之为原型的平台。这样，您就定义了全局属性或数据对象，告诉 Vue 记住它是一个整体，然后用一行代码在任何组件中访问它。

### 原型语法

主 JavaScript 文件中 Vue 原型的定义如下:

```
Vue.prototype.$blogName = ‘LogRocket’ 
```

Enter fullscreen mode Exit fullscreen mode

这里，`blogName`是属性或数据名，`LogRocket`字符串是值。有了这个定义，Vue.js 就可以让整个项目中的每个 Vue 实例使用这个属性(在我们的例子中是博客名),甚至在实例创建之前。

### [T2`$`星座](#the-raw-endraw-sign)

在 Vue.js 中使用`$`符号来标识可以在任何给定 Vue 项目的所有可用实例中使用的属性。这种区分是由于与属性名称定义和访问相关的冲突。有了`$`符号，所有实例可用的属性和全局属性之间就有了明显的区别。

### 演示

要完成这一部分，您必须从头开始阅读这篇文章，从上面的链接下载 starter 项目，并在 VS 代码中打开它。为了说明上一节中的语法示例，打开您的`main.js`文件并添加原型定义，这样整个文件看起来像这样:

```
import Vue from 'vue'
import App from './App.vue'
Vue.config.productionTip = false
Vue.prototype.$blogName = 'LogRocket'
new Vue({
  render: h => h(App),
}).$mount('#app') 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经定义了一个属性名，打开您的根组件并复制下面的代码块:

```
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <Test msg="Welcome to Your Vue.js App"/>
  </div>
</template>
<script>
import Test from './components/Test.vue'
export default {
  name: 'app',
  components: {
    Test
  },
   beforeCreate: function() {
    console.log(this.$blogName)
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这里你可以看到,`beforeCreate`生命周期钩子方法被用来验证关于 Vue 原型的实例化声明。如果您在您的开发服务器上运行该应用程序，您将会看到保存的名称(LogRocket)显示在您的浏览器控制台中。

## 一些 Vue 原型用例

许多数据值、属性和实用程序(如 HTTP 资源)可以通过 Vue 原型成为全局属性。在这一节中，我将介绍其中的几个。

### 功能为原型

Vue.js 不仅允许添加属性，还允许添加方法作为原型。这样，使用原型的每个实例都可以访问原型定义中设置的逻辑。

这包括使用`this`访问数据、计算属性，甚至项目中任何 Vue 实例内的其他方法。一个简单的例子就是使用字符串反转函数。在您的`main.js`文件中，在旧的原型下添加新的原型:

```
Vue.prototype.$reverseString = function(x) {
  this = this
    .split('')
    .reverse()
    .join('')
} 
```

Enter fullscreen mode Exit fullscreen mode

将下面的代码块复制到您的`app.vue`文件:

```
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <Test msg="Welcome to Your Vue.js App"/>
  </div>
</template>
<script>
import Test from './components/Test.vue'
export default {
  name: 'app',
  components: {
    Test
  },
  data() {
    return {
      msg: 'LogRocket'
    }
  },
   beforeCreate: function() {
    console.log(this.$blogName)
  },
  created: function() {
    console.log(this.msg) 
    this.$reverseString('msg')
    console.log(this.msg) 
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这里，创建的生命周期钩子被用来记录`reverseString`函数，如果您在 dev 服务器中运行您的应用程序，您将看到 LogRocket 被反向打印。

### 进口原型机

如果您的应用程序与第三方 API 进行通信，您通常必须在您想要发出`get`请求的每个组件上导入 Axios。下面是一个简单的例子——在 VS 代码应用程序中打开一个新的终端并安装 Axios:

```
npm install axios 
```

Enter fullscreen mode Exit fullscreen mode

现在，转到您想要发出`get`请求的`app.vue`文件，并将这段代码块复制到:
中

```
<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <Test msg="Welcome to Your Vue.js App"/>
    <div v-for="user in users" :key="user.id">
      <p>{{user.name}}</p>
    </div>
  </div>
</template>
<script>
import Test from './components/Test.vue'
import axios from 'axios'
export default {
  name: 'app',
  components: {
    Test
  },
  data() {
    return {
      msg: 'LogRocket',
      users: []
    }
  },
  created: function (){
      axios.get('https://jsonplaceholder.typicode.com/users')
      .then(res => {
        this.users = res.data;
      })
  }
  ,
   beforeCreate: function() {
    console.log(this.$blogName)
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

您将会注意到，对于您想要发出 get 请求的每个组件，您都必须重复这个 import 语句。为了解决这个问题，Vue 允许您使用 prototype 特性一次性导入项目中的任何 Vue 实例。

打开您的`main.js`文件，并将下面的代码块复制到其中:

```
import Vue from 'vue'
import App from './App.vue'
import axios from 'axios'
Vue.config.productionTip = false
Vue.prototype.$blogName = 'LogRocket'
Vue.prototype.$reverseString = function(x) {
  this = this
    .split('')
    .reverse()
    .join('')
}
Vue.prototype.$axios = axios
new Vue({
  render: h => h(App),
}).$mount('#app') 
```

Enter fullscreen mode Exit fullscreen mode

在您的`app.vue`文件中，删除 import 语句并使用您已经创建的`$axios`别名，如下所示:

```
<script>
import Test from './components/Test.vue'
export default {
  name: 'app',
  components: {
    Test
  },
  data() {
    return {
      msg: 'LogRocket',
      users: []
    }
  },
  created: function (){
      this.$axios.get('https://jsonplaceholder.typicode.com/users')
      .then(res => {
        this.users = res.data;
      })
  }
  ,
   beforeCreate: function() {
    console.log(this.$blogName)
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

[![Completed List Import Demo](img/8c5e4164a19a71c5dca8814ed835e4c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PgK-cxw4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2019/08/completed-list-import-e1565708685861.png%3Fresize%3D730%252C377%26ssl%3D1)

你可以在 GitHub 上找到本教程[的完整代码。](https://github.com/viclotana/vue_prototypes)

## 结论

现在，您已经了解了在 Vue 中使用原型的概念，以使属性对项目中的所有 Vue 实例全局可用。还向您展示了使用原型的优势，包括但不限于更高效的工作流。黑客快乐！

* * *

**编者按:**看到这个帖子有问题？你可以在这里找到正确的版本。

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[用原型](https://blog.logrocket.com/accessing-properties-globally-in-vue-js-with-prototypes/)在 Vue.js 中全局访问属性最先出现在 [LogRocket 博客](https://blog.logrocket.com)上。
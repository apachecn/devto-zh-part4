# Vue.js 上的简单天气 App

> 原文：<https://dev.to/oxyyyyy/simple-weather-app-on-vue-js-1g20>

你好。我现在正在学习 Vue。因此，我认为*全面了解事物的最佳方式是写一篇关于它们的文章* :]
此外，当我开发应用程序时，我意识到缺少我将要谈到的信息。

我将概述我的宠物项目 Vue 应用程序，该应用程序使用来自 [Weatherbit](https://www.weatherbit.io/) 的公共 API，并且只获得基于城市的天气:温度和描述。

app 有两种视图:
**首页**
[![Home view](img/2ca0721aca490e62c22996f4579062f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1ZrqT7OG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dx5y7pie2kskzkmke2hh.png) 
**关于**
[![About page](img/33127b137c049cdda8ca057e7b7c6cf6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wtoK7V5S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qtb1tosdn47d3ucag5u1.png)
**本文中我不打算用**来描述每一笔代码。相反，**我会把重点放在**app 的架构和流程上。

> 你可能需要有 HTML、CSS & JS 的基础知识，以及 Vue.js 的概念，才能完全理解这里发生的事情。

#### 我用过的技术:

*   存储数据的 Vuex。
*   从 API 获取数据的 Axios。
*   导航用的路由器扔 app。实际上，在这个项目中没有必要使用它，但我只是想用行动来展示它。

该项目由 [Vue CLI](https://cli.vuejs.org/) 生成和管理。
我强烈推荐你使用这台仪器。非常适合初学者。

## 我们来看看项目的架构。

所以`src`文件夹包含:

```
src
│
├───assets # Stuff like images are stored here
│       logo.png
│
├── components # Independent "bricks" of app
│       weatherWidget.vue
│
├── services # Code that works with API. Axios gets data from API here
│       service.js
│
├── views # Views are like pages. Router navigates through them
│       About.vue
│       Home.vue
├── App.vue # Root Vue component
├── constants.js # Just some constants that I want to store
├── main.js # Core JS file that imports and mounts our app
├── router.js # Router config is here
├── store.js # Store management with Vuex 
```

现在让我们更仔细地研究代码！

#### main.js🚩

从`main.js`开始。
`main.js`是整个项目的根 javascript 文件。
我们正在导入核心库配置和组件，然后创建`new Vue`实例并告诉 Vue 使用`router`和`store`。

```
import Vue from "vue"; // Vue lib
import App from "./App.vue"; // Our root component
import router from "./router"; // Router config
import store from "./store"; // Store config

import "normalize.css"; // Normalize.css lib to reset default styles

Vue.config.productionTip = false;

new Vue({
  router,
  store,
  render: h => h(App)
}).$mount("#app"); // Create Vue instance and mount it in index.html -> #app element 
```

#### [T1】constants . js📄](#constantsjs)

这是存储我的 API 密钥和 URL 的地方。
可以从 REST API 中取出很多数据。查看[文档](https://www.weatherbit.io/api)开始研究。
在这个项目中，我将获取我的家乡基辅的当前天气。因此，带有 API 请求的查询和关键字的 URL 将类似于`CURRENT_WEATHER`。

```
const API_KEY = "b60f3577e8eb46f089853e2a9fd7d744";
const CURRENT_WEATHER = `https://api.weatherbit.io/v2.0/current?city=Kiev,UA&key=${API_KEY}`;

export { API_KEY, CURRENT_WEATHER }; // export constant to be able to use them in components 
```

#### router.js🔀

路由器配置。该应用程序有两个视图(页面)-主页和关于。所以我希望有这样的网址:`https://app`和`https://app/about`。我可以在`router.js`中定义它们。我所需要的就是指定`Router`实例的`routes`中的每个页面:写下路径，给出路由的名称，并将其与现有组件链接。注意，`/about`视图将被延迟加载。
`mode: "history"`表示路由在 URL 中将没有`#`。没有这一行，每一个 URL 都会是这个样子:`https://app/#route`。但是不要忘记[配置](https://router.vuejs.org/guide/essentials/history-mode.html)你的服务器，让它在历史模式下正常工作。

```
import Vue from "vue";
import Router from "vue-router";
import Home from "./views/Home.vue"; // import components that you wish to became Routes

Vue.use(Router); // tell Vue to action with Router

export default new Router({
  mode: "history",
  base: process.env.BASE_URL,
  routes: [ // All the routes are described here
    {
      path: "/",
      name: "home",
      component: Home
    },
    {
      path: "/about",
      name: "about",
      // route level code-splitting
      // this generates a separate chunk (about.[hash].js) for this route
      // which is lazy-loaded when the route is visited.
      component: () =>
        import(/* webpackChunkName: "about" */ "./views/About.vue")
    }
  ]
}); 
```

#### store.js 🗃️

`Store`管理。
`Store`包含全局数据 app 的状态。
这里设置 app 的`state`，定义`mutations` & `actions`。

如果你简化一下，处理`Store`的算法是:
我们称一个`action` ➡️动作调用一个`mutation` ➡️ `mutation`改变`state`

*注意:`import`路径中的* `@`表示从我们工作区的根目录`src`文件夹开始。

```
import Vue from "vue";
import Vuex from "vuex";

import service from "@/services/service.js"; // service.js fetch data from API. We will have a look at it in the next step.

Vue.use(Vuex); // tell Vue to action with Vuex

export default new Vuex.Store({
  state: { // define here data that you wish to store
    weather: {},
    dataIsRecived: false
  },
  mutations: { // change state from here
    UPDATE_WEATHER(state) {
      service
        .getWeather() // call the function from service.js that returns the data from API
        .then(response => { // if the response was get
          state.weather = response.data.data[0]; // set weather obj in state to real weather obj
          state.dataIsRecived = true; // mark that data was recived
          console.log(response); // and log it
        })
        .catch(error => { // if there was an error
          console.log("There was an error:", error.response); // log it
          state.dataIsRecived = false; // and mark that data wasn't recived
        });
    }
  },
  actions: { // call mutations that change the state here
    updateWeather(context) {
      context.commit("UPDATE_WEATHER");
    }
  }
}); 
```

#### 服务/服务。js□

与 API 的通信。这里用的是
`Axios`。基于 Promise 的 HTTP 请求到达 Weatherbit 并获得当前天气的真实数据。

```
import axios from "axios";
import { CURRENT_WEATHER } from "@/constants"; // URL with queries and API key

const apiClient = axios.create({ // create promise
  baseURL: CURRENT_WEATHER,
  withCredentials: false, // CORS
  headers: { // some HTTP headers
    Accept: "application/json",
    "Content-Type": "application/json"
  }
});

export default {
  getWeather() { // function that is used in store.js 👆
    return apiClient.get();
  }
}; 
```

### 接下来是什么？

实际上，现在我们已经拥有了编写 Vue 组件并在其中使用所有这些东西的一切。那就让我们开始吧！

#### app . view

根 Vue 组件。
此处使用路由器添加导航栏，链接到主页和关于视图。

```
<template>
  <div id="app"> // root
    <div class="nav"> // navbar
      <router-link to="/" class="nav__link">Home</router-link>
      <router-link to="/about" class="nav__link">About</router-link>
    </div>
    <router-view /> // router views will be rendered here
  </div> </template> 
<style lang="scss"> // some styles 🖍️
  @import url('https://fonts.googleapis.com/css?family=Montserrat:100,200,300,400&display=swap&subset=cyrillic');

  body {
    font-family: 'Montserrat', sans-serif;
    max-height: 100vh;
  }

  a {
    color: #153B50;
    text-decoration-color: rgba($color: #153B50, $alpha: 0.5);
    transition: all 0.3s ease;

    &:hover {
      text-decoration-color: #153B50;
    }
  }

  .nav {
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 15px 0;
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
  }

  .nav__link {
    &:not(:last-child) {
      margin-right: 15px;
    }
  }
</style> 
```

#### 视图/About.vue

只有占位符的视图。

```
<template>
  <div class="about">
    <p>Thanks <a href="https://www.weatherbit.io/">Weatherbit</a> for public API!</p>
  </div> </template> 
<style lang="scss" scoped> // some styles 🖍️
  .about {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }
</style> 
```

#### 视图/Home.vue

具有 weatherWidget vue 组件的视图。请看下一部分。

```
<template>
  <div class="home">
    <weatherWidget />
  </div> </template> 
<script>
import weatherWidget from '@/components/weatherWidget.vue'; // import component

export default {
  name: "home",
  components: { // and register it
    weatherWidget
  }
}
</script> 
<style lang="scss" scoped> // some styles 🖍️
  .home {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }
</style> 
```

#### 组件/weatherWidget.vue

*于是神奇就在这里发生了。*
我们已经准备好获取天气数据并在组件中呈现它。
我们现在要做的只是`dispatch`存储`action`(这将调用突变，这将调用服务并将从 API 捕获的数据写入`state`)。
与生命周期挂钩`created`我们称之为`updateWeather` `action`。然后我们有了`computed property`天气，它保存了状态和组件之间的反应。
最后一件事:我们必须将计算的属性插入到组件中。
还有，有某种预装器。当`dataIsRecived`状态属性为`false`(查看 store.js)时，SVG 微调器正在旋转并等待数据。

```
<template>
  <div>
    <div v-if="this.$store.state.dataIsRecived" class="weather-widget"> // widget itself
      <p class="weather-widget__city">{{ weather.city_name }}</p>
      <h2 class="weather-widget__temp">{{ weather.temp }}<span>°C</span></h2>
      <p class="weather-widget__status">{{ weather.weather.description }}</p>
    </div>
    <div v-else class="weather-widget"> // preloader
      <img src="spinner.svg" alt="">
    </div>
  </div> </template> 
<script>
  export default {
    computed: {
      weather() {
        return this.$store.state.weather // gets weather state from Vuex store
      }
    },
    created() {
      this.$store.dispatch("updateWeather"); // dispatch "updateWeather" when component is created
    }
  }
</script> 
<style lang="scss" scoped> // some styles 🖍️
  .weather-widget {
    display: flex;
    flex-direction: column;
    align-items: center;
    color: #429EA6;
  }

  .weather-widget__city {
    font-size: 20px;
    margin: 0;
  }

  .weather-widget__temp {
    display: flex;
    align-items: flex-start;
    color: #16F4D0;
    font-size: 200px;
    font-weight: 200;
    margin: 0;

    span {
      font-size: 30px;
      font-weight: 400;
      margin-top: 35px;
    }
  }

  .weather-widget__status {
    font-size: 20px;
    margin: 0;
  }
</style> 
```

## 就是这样！

我们的 Vue SPA 有效！它从 Weatherbit 获取数据并将其显示在屏幕上。简单。

你可以在我的 GitHub 库中找到完整的源代码。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[oxyyyyy](https://github.com/oxyyyyy)/[vue-天气](https://github.com/oxyyyyy/vue-weather)

### 用 Vue 和公共天气 API 制作的天气 app

<article class="markdown-body entry-content container-lg" itemprop="text">

# Vue 天气应用

> 关于它的[篇](https://dev.to/oxyyyyy/simple-weather-app-on-vue-js-1g20)。

[![Vue Weather App](img/71f7c024241e72c5ccd3813008e2b32d.png)](https://camo.githubusercontent.com/1fd7091de81edf0c9a4daa0ff72ff97325a9413e/68747470733a2f2f74686570726163746963616c6465762e73332e616d617a6f6e6177732e636f6d2f692f79636d76357a6168636e687779666d346833646c2e706e67)

## 项目设置

```
yarn install 
```

### 为开发进行编译和热重新加载

```
yarn run serve 
```

### 为生产进行编译和精简

```
yarn run build 
```

### 运行您的测试

```
yarn run test 
```

### 链接并修复文件

```
yarn run lint 
```

### 自定义配置

参见[配置参考](https://cli.vuejs.org/config/)。

</article>

[View on GitHub](https://github.com/oxyyyyy/vue-weather)

我期待着你们的反馈，伙计们，这对我很重要。这是我的第一篇文章，我很高兴能为 DEV.to 社区做出自己的贡献。我希望这篇文章对某人有所帮助😉
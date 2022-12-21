# Vue 中的动态菜单

> 原文：<https://dev.to/ptheodosiou/dynamic-menu-in-vue-1cc9>

我们都使用 Vue 创建了一个 web 应用程序，当然这些应用程序中有一个菜单。

主要的想法是在我开发一个辅助项目时产生的，我必须创建太多的路线和子路线。所以，我决定创建这个教程，向你展示我是如何解决我的问题的。

* * *

### 我们开始吧

首先，用 Vue CLI
创建一个项目

```
vue create dynamic-menu-vue 
```

Enter fullscreen mode Exit fullscreen mode

```
Vue CLI v3.11.0
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Router, CSS Pre-processors, Linter
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported by default): Sass/SCSS (with node-sass)
? Pick a linter / formatter config: Basic
? Pick additional lint features: (Press <space> to select, <a> to toggle
all, <i> to invert selection)Lint on save
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? In package.json
? Save this as a preset for future projects? No 
```

Enter fullscreen mode Exit fullscreen mode

一旦你的项目准备好了，你就可以像平常一样服务它了

```
npm run serve -- --open 
```

Enter fullscreen mode Exit fullscreen mode

[查看源代码](https://github.com/ThPadelis/dynamic-menu-vue)

[观看现场演示](https://thpadelis.github.io/dynamic-menu-vue/)

* * *

让我们来分解一下我为`src/router.js`
写的代码

```
import Vue from "vue";
import Router from "vue-router";

Vue.use(Router);

export default new Router({
  mode: "history",
  base: "/dynamic-menu-vue/",
  routes: [
    {
      path: "/",
      redirect: { path: "/home" },
      meta: {
        visible: false
      }
    },
    {
      path: "/home",
      name: "home",
      component: () =>
        import(/* webpackChunkName: "home" */ "./views/Home.vue"),
      meta: {
        visible: true
      },
      children: [
        {
          path: "sub-view-1",
          name: "sub-view-1",
          component: () =>
            import(
              /* webpackChunkName: "home-sub-view-1" */ "./components/Home/SubView1.vue"
            ),
          meta: {
            visible: true
          }
        },
        {
          path: "sub-view-2",
          name: "sub-view-2",
          component: () =>
            import(
              /* webpackChunkName: "home-sub-view-2" */ "./components/Home/SubView2.vue"
            ),
          meta: {
            visible: true
          }
        }
      ]
    },
    {
      path: "/about",
      name: "about",
      component: () =>
        import(/* webpackChunkName: "about" */ "./views/About.vue"),
      meta: {
        visible: true
      }
    },
    {
      path: "*",
      name: "not-found",
      component: () =>
        import(/* webpackChunkName: "not-found" */ "./views/NotFound.vue"),
      meta: {
        visible: false  
      }
    }
  ]
}); 
```

Enter fullscreen mode Exit fullscreen mode

*   **meta** :该属性用于增强路由。在这种情况下，我们只是用它来使路线在菜单上可见，
*   **延迟加载**:在这个项目中没有使用延迟加载的实际需要，但是这是一个减小包大小的好技巧，
*   **base** :我设置这个基本 URL 是为了在 GitHub 页面上部署这个项目。

* * *

好了，现在我们有了路线，我们需要创建菜单组件。这一次我将使用 bootstrap。让我们将 bootstrap 添加到项目中。

```
npm install bootstrap --save 
```

Enter fullscreen mode Exit fullscreen mode

然后在`src`文件夹下创建一个名为`styles.scss`的新文件，并添加这些代码行

```
@import "./assets/variables";
@import "node_modules/bootstrap/scss/bootstrap.scss";
@import "./assets/bootswatch"; 
```

Enter fullscreen mode Exit fullscreen mode

现在，在`main.js`
中加上`styles.scss`

```
...
import "./styles.scss";
... 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 菜单组件

在`src/components`下创建一个名为 Menu 的新文件夹，并在其中创建两个新文件。

1.  `Navbar.vue`
2.  `MenuItem.vue`

让我们继续看`Navbar.vue`和

```
<template>
  <nav class="nav flex-column p-3">
    <menu-item v-for="(r,i) in routes" :key="i" :route="r"></menu-item>
  </nav>
</template>

<script>
export default {
  name: "navbar",
  components: {
    MenuItem: () => import(/* webpackChunkName: "menu-item" */ "./MenuItem")
  },
  computed: {
    routes() {
      return this.$router.options.routes;
    }
  }
};
</script>

<style>
</style> 
```

Enter fullscreen mode Exit fullscreen mode

计算属性`routes()`返回`router.js`文件的内容。

然后是`MenuItem.vue`

```
<template>
  <div>
    <li v-if="isVisible" class="nav-item rounded shadow-sm mb-2">
      <router-link
        exact-active-class="text-success"
        :to="{name: route.name}"
        class="nav-link"
      >{{name}}</router-link>
    </li>

    <div v-if="route.children && route.children.length">
      <menu-item v-for="(r,i) in route.children" :key="i" :route="r" class="ml-3"></menu-item>
    </div>
  </div>
</template>

<script>
export default {
  name: "menu-item",
  props: {
    route: {
      type: Object
    }
  },
  computed: {
    isVisible() {
      if (
        this.route.meta &&
        (this.route.meta.visible === undefined || this.route.meta.visible)
      ) {
        return true;
      }
      return false;
    },
    name() {
      return this.route.name
        .toLowerCase()
        .split("-")
        .map(s => s.charAt(0).toUpperCase() + s.slice(1))
        .join("  ");
    }
  }
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

性能

*   `route`来自父元素`Navbar.vue`的确切路线

计算属性

*   如果菜单上应该有路线，则返回 true，否则返回 false
*   `name()`返回路线的名称

活动路线类别

*   这将向活动菜单项添加一个类别。

正如您所注意到的，如果路由有子组件，我们有一个对同一个组件的递归调用。

* * *

目前就这些。如果你有任何问题让我知道，我将很乐意帮忙。

编码快乐！
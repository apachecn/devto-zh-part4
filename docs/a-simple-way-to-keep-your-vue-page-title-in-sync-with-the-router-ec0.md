# 让您的 Vue 页面标题与路由器保持同步的简单方法

> 原文：<https://dev.to/webhookrelay/a-simple-way-to-keep-your-vue-page-title-in-sync-with-the-router-ec0>

我注意到在相当多的项目中，开发人员忘记用路由器更新页面标题，或者可能认为他们明天会这样做，因为这是一个很小的功能:)。出于几个原因，保持标题与内容同步总是有意义的:

*   帮助用户使用多个选项卡
*   对网站分析很重要

我将向您展示如何使用标准的 [vue 路由器](https://github.com/vuejs/vue-router)来实现这一点。

## 步骤 1:在路由器配置中声明路由元

首先，让我们给我们的标准路线添加一些额外的元数据:

```
 routes: [
    {
      path: '/',
      name: 'home',
      component: HomePage,
      meta: {
        auth: true,
        title: 'Dashboard'
      }
    }, {
      path: '/login',
      name: 'login',
      component: LoginPage,
      meta: {
        auth: false,
        title: 'Login'
      }
    }, {
      path: '/buckets',
      name: 'buckets',
      component: BucketsPage,
      meta: { auth: true, title: 'Buckets' } 
```

Enter fullscreen mode Exit fullscreen mode

更多关于路由器包装的文档可以在 vue 路由器官方网站找到[。](https://router.vuejs.org/guide/essentials/dynamic-matching.html#reacting-to-params-changes)

## 第二步:在你的 App.vue 中添加$route watcher

去你的 Vue 主页。vue 文件(它应该有一个`<router-view></router-view>`组件)并添加一个观察器:

```
 watch: {
      '$route' (to, from) {
        document.title = to.meta.title || 'Your Website'
      }
    }, 
```

Enter fullscreen mode Exit fullscreen mode

这将设置一个标题作为您的页面标题(如果您为该路由指定了元字段)。我曾试图用路由器导航卫士达到同样的效果，但`$route` watcher 似乎是最简单的解决方案。

看起来很简单，对吧？:)在路由器元中随意试验和定义更多的字段，如页面描述和您希望为该页面设置的任何其他内容。然后修改观察器也使用它们。

希望这有所帮助！
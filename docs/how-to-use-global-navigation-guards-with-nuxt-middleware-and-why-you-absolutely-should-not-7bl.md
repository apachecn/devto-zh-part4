# 如何在 Nuxt 中间件中使用全球导航卫士，以及为什么绝对不应该这样做

> 原文：<https://dev.to/husteadrobert/how-to-use-global-navigation-guards-with-nuxt-middleware-and-why-you-absolutely-should-not-7bl>

TLDR:

*   通过使用中间件，可以在 Nuxt 中使用全球导航卫士
*   在中间件中，您可以访问上下文，通过它您可以直接访问 Vue 路由器。
*   通过添加全局导航保护，每次使用路由器时都会运行该代码，从而导致意想不到的后果
*   相反，在页面中使用组件内导航保护

我对 Vue 和 Nuxt 还很陌生，但是因为我们在工作中使用它，所以我试着边走边学。我们正在构建一个类似于 Pivotal Tracker 或 Trello 的任务管理系统。我们有列表，在这些列表中我们有任务，一个任务显示它的任务细节。通过单击列表中的任务，您可以更改要显示的任务详细信息。我面对的是我最初认为很容易的任务。

任务:当用户在 TaskDetail 中编辑文本框时，通过添加确认弹出窗口，确保用户不会意外地离开页面(并丢失他们的编辑)。

输入导航守卫([导航守卫| Vue 路由器](https://router.vuejs.org/guide/advanced/navigation-guards.html))，它“主要用于通过重定向或取消导航来守卫导航。”通过使用全球导航保护，我可以确保每当 Vue 路由器改变路由时(当有人点击不同的任务时)，我可以检查他们是否正在编辑文本框，然后使用确认弹出窗口。

大概是这样:

```
const router = new VueRouter({ ... })

router.beforeEach((to, from, next) => {
  if(confirm("Are you sure?")) {
    next()
  } else {
    next(false)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

问题:我们使用的是 Nuxt，它会根据你的文件结构自动设置 Vue 路由器。但是一定有办法把全球导航卫士和 Nuxt 一起用吧？

我谷歌了一下，我所能找到的解决这个问题的方法是[一个孤独的堆栈溢出问题](https://stackoverflow.com/questions/53322525/how-to-set-beforeresolve-navigation-guard-in-nuxt-js)，它没有答案，但至少是一个有用的评论，关于中间件是如何设计的。

## Nuxt 中的中间件是什么？

"中间件让你定义自定义函数，这些函数可以在呈现一个页面或一组页面之前运行."( [Routing - Nuxt.js](https://nuxtjs.org/guide/routing#middleware) )同样重要的是，中间件可以访问上下文( [API: The Context - Nuxt.js](https://nuxtjs.org/api/context) )，可以访问商店，商店可以访问 Vue 路由器！

所以我的想法现在很简单:创建一个直接访问路由器的中间件，添加一个全局导航保护，这样每当路由器改变页面时，它都会与用户确认他们是否真的想移动。我们开始吧！

对于我的中间件，我创建了这个基本文件

```
in check-before-move.js

export default function ({ store }) {
  store.app.router.beforeEach((to, from, next) => {
    if(confirm("Are you sure?")) {
      next()
    } else {
      next(false)
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

然后将其添加到 nuxt
的配置中

```
in nuxt.config.js

  router: {
    middleware: "check-before-move"
  } 
```

Enter fullscreen mode Exit fullscreen mode

这导致了一个棘手小问题。你能发现它吗？如果我的中间件真的在路由器每次运行时都运行，会发生什么？

没错，每次路由器运行时，我都会添加另一个全球导航卫士。我的用户必须确认一次，然后两次，然后三次..每次他们导航到不同的任务。记住，每次路由器移动时，中间件文件从头到尾都在运行。每当中间件文件运行时，我们都在做另一个`beforeEach`。现在看着很明显，但是当时我花了很长时间才想明白！

所以我愚蠢的大脑说，“哦，好吧，我只需要添加一个检查来看看我是否已经设置好了”，然后我立刻把我的代码改成:

```
in check-before-move.js

export default function ({ store }) {
  let setup = false
  if(!setup) {
    store.app.router.beforeEach((to, from, next) => {
      setup = true
      if(confirm("Are you sure?")) {
        next()
      } else {
        next(false)
      }
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所想象的，这也不起作用(阅读上面我说的代码每次都从头到尾运行，你就会明白为什么我的主计划失败了)

因此，正如我们所看到的，是的，你可以通过使用中间件在 Nuxt 中添加一个全局导航保护，但是你不应该这样做(或者至少在实现它之前要非常确定你在做什么)。

## 后记

对于其他任何人来说，如果他们想知道如何在自己的项目中实现相同的特性，并且像我一样痛苦，我将告诉你我是如何设法实现一个解决方案的。我没有走向全球，而是引入了一个组件内防护。但是要小心，那些组件内防护只对页面有效，对组件无效(尽管页面是组件，但是 Nuxt 对这些事情有自己的看法)，所以在这里查看组件内防护([Navigation Guards | Vue Router](https://router.vuejs.org/guide/advanced/navigation-guards.html#in-component-guards))

祝你好运，并快乐编码！

(注意:正如我之前所说的，我是 Vue/Nuxt 的新手，所以如果你发现了任何错误，请随时指出来！)
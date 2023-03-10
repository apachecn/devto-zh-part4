# 理解 Nuxt & Vue 挂钩和生命周期(第 2 部分)

> 原文：<https://dev.to/lilianaziolek/understanding-nuxt-amp-vue-hooks-and-lifecycle-part-2-mmc>

这是迷你系列的第 2 部分——理解 Nuxt & Vue 钩子和生命周期。你可以从[第 1 部分开始，这里是](https://dev.to/lilianaziolek/understanding-nuxt-vue-hooks-and-lifecycle-part-1-48lc)，以确保你至少模糊地熟悉大部分需要的概念。如果你有其他编程背景，但不是 Vue/Nuxt，你可能也会发现[我的另一篇文章](https://dev.to/lilianaziolek/a-super-quick-guide-to-vuejs-ecosystem-from-senior-java-dev-point-of-view-1kmo)很有用。

# app 里有什么

[示例代码](https://github.com/lilianaziolek/blog-examples/blob/master/dry-examples)包含了第 1 部分中讨论的所有机制/挂钩的非常简单的例子。为了让这篇文章有意义，尤其是“它是如何工作的”部分，你需要**下载它并在本地运行**来跟随。

值得注意的文件:

*   [左分量。视图](https://github.com/lilianaziolek/blog-examples/blob/master/dry-examples/components/LinksComponent.vue)
    *   包含项目中各种链接的(硬编码)列表，以允许用户导航。
    *   包括**mixin**[logroutequeryandparams . js](https://github.com/lilianaziolek/blog-examples/blob/master/dry-examples/mixins/logRouteQueryAndParams.js)。它演示了 mixin(计算属性 *routeParams* )中的内容以与组件中直接定义的内容相同的方式执行，并且 mixin 代码可以访问 *this* 。
    *   显示了大多数 Vue 组件生命周期方法
*   [globalMiddleware.js](https://github.com/lilianaziolek/blog-examples/blob/master/dry-examples/middleware/globalMiddleware.js) 和[local middleware . js](https://github.com/lilianaziolek/blog-examples/blob/master/dry-examples/middleware/localMiddleware.js)——顾名思义，全局中间件从`nuxt.config.js`开始附加，因此在每条路由之前执行，而本地中间件只包含在`test1/_param1/_param2`路由中。
*   几条路线(页数):
    *   [index.vue](https://github.com/lilianaziolek/blog-examples/blob/master/dry-examples/pages/index.vue) -起点，包含 point 组件
    *   `test1/param1?/param2?` -一条路径，有两个可选参数，表示`test1/`、`test1/lorem`和`test1/lorem/ipsum`都将到达 [_param2.vue](https://github.com/lilianaziolek/blog-examples/blob/master/dry-examples/pages/test1/_param1/_param2.vue) 文件中代码生成的页面
    *   `test1/param1?/param2?` -相当于 test1 route 的一个 route，它表明如果你不喜欢用 route 的最后一个参数的名字来命名你的 vue 文件，你可以把它们放在子目录中，命名为 [index.vue](https://github.com/lilianaziolek/blog-examples/blob/master/dry-examples/pages/test2/_param1/_param2/index.vue)
    *   `foo/x/_id?`和`foo/y/_id?` -显示动态嵌套路由如何工作。嵌套路由是页面包含另一个`router-view`组件的地方，就像在 [foo.vue](https://github.com/lilianaziolek/blog-examples/blob/master/dry-examples/pages/foo.vue) 中一样。默认情况下，Nuxt 总会有一个(您不需要显式包含它，Nuxt 会为您完成)，所以这实际上是一个路由器中的路由器。故名，**嵌套**。

# 这一切是怎么运作的？

让我们假设用户首先导航到我们的主页(例如 [http://localhost:3000](http://localhost:3000) )，然后通过点击适当的链接导航到各种其他页面。如果您想观察 SPA 模式的运行，您需要点击链接，而不是直接在浏览器中放置 URL。这是因为从地址栏导航会强制 SSR 模式。

让我们来看一个用户旅程示例:

## (初诊) [http://localhost:3000](http://localhost:3000)

### 日志里有什么？

*在服务器上，在回答返回给客户端之前:*

```
(AlternativeEventBus Plugin) SSR: true inject component with id: 4
(NuxtServerInit) SSR: true
(Global Middleware) SSR: true
(LinksComponent) SSR: true [BeforeCreate]
(LinksComponent) SSR: true [Created] SampleProp: Prop from main page, SampleData: Lorem Ipsum Data
(LinksComponent) Created Refs: 
```

*在客户端(浏览器)端:*

```
(EventBus Plugin) SSR: false inject component with id: 1
(AlternativeEventBus Plugin) SSR: false inject component with id: 2
(LinksComponent) SSR: false [BeforeCreate]
(LinksComponent) SSR: false [Created] SampleProp: Prop from main page, SampleData: Lorem Ipsum Data
(LinksComponent) Created Refs:
(LinksComponent) SSR: false [Mounted] SampleProp: Prop from main page, SampleData: Lorem Ipsum Data
(LinksComponent) Mounted Refs: Foo With No Params,Foo X With Param1,(...) 
```

### 刚刚发生了什么？

*   在这个调用中，globalMiddleware 只在 SSR 中执行
*   *AlternativeEventBus 插件*设置在两端(客户端和服务器端)
*   *EventBus 插件*只在客户端设置
*   在服务器和客户端上都调用 beforeCreate 和 created
*   仅在客户端上调用 Mounted
*   这个。$refs 仅在挂载时填充

### 我们哪里出错了？

假设您在中间件或 fetch 中的某个地方有代码，并且您注册了`this.$eventBus.$on`事件监听器。然后，基于一些用户交互，您通过`this.$eventBus.$emit`分派一个事件。事不工，听者不叫——为什么？

您可能会注意到， *AlternativeEventBus 插件* **id** 在客户端和服务器上是不同的(如果您的情况不是这样，请刷新页面，因为服务器上的 id 将在后续的 SSR 调用中发生变化)。那是因为这个插件的代码在客户端和服务器端都被执行，**双方都创建了一个对象**。中间件和 fetch 只在第一次调用时在 SRR 执行，所以您的**监听器**在 eventBus 的 SSR 实例上注册。客户端交互代码在浏览器中运行，因此您的**发出事件**在 eventBus 的客户端实例上触发。不一样的情况——交流没有发生。

eventBus 的主要用途是让代码的一部分通知另一部分发生了一些事情。这允许我们编写一个**更加解耦的应用**。例如，您的登录代码可以发布一个用户刚刚登录的事件，以便代码的其他部分可以做出反应，将额外的用户数据提取到 VueX 中。这样，登录代码本身不需要知道应用程序其他部分所需的任何用户数据。

因此，在现实中，这样的 eventBus 插件在 dual (SSR/client)模式下可能没有意义。如果交互总是发生在浏览器中，那么只在客户端使用这样的插件更有意义。这样，如果有人试图在 SSR 代码中注册一个事件监听器，他们将得到一个很好的错误，指出 eventBus 是未定义的——而不是允许他们在一个永远不会接收任何事件的实例上注册。

## 点击链接`Test1 with Param 1`

### 日志里有什么？

*在此，下面所有的调用都只发生在客户端(浏览器)端:*

```
(Global Middleware) SSR: false
(Local Middleware) SSR: false
(Mixin) /test1/val1AsyncData: {"param1":"val1"}
(Mixin) /test1/val1Fetch: {"param1":"val1"}
(LinksComponent) SSR: false [BeforeCreate]
(LinksComponent) SSR: false [Created] SampleProp: Test1, SampleData: Lorem Ipsum Data
(LinksComponent) Created Refs: 
(LinksComponent) SSR: false [Mounted] SampleProp: Test1, SampleData: Lorem Ipsum Data
(LinksComponent) Mounted Refs: Foo With No Params,Foo X With Param1,(...) 
```

### 刚刚发生了什么？

*   全局中间件和现在的本地中间件都在客户端处理。
*   现在调用来自 *logRouteQueryAndParams* 的 Mixin 代码用于获取和异步数据
*   LinksComponent 中的所有 Vue 生命周期挂钩都被再次调用。路由已经更改，index.vue 中使用的 LinksComponent 实例现在将被销毁，并创建一个新实例(用于 test1 路由)

### 我们哪里出错了？

主页上没有调用 Fetch 和 asyncData，但它在此页面上，为什么？那是因为 index.vue 没有把它作为 mixin 包含进来，而 _param2.vue 包含了。LinksComponent 也包含这个 mixin，但是没有为组件调用 **asyncData 和 fetch。如果您遇到数据似乎没有填充到 UI 中的情况，请始终仔细检查您的获取代码是否在页面中，而不是在组件中。**

## 点击链接`Test2 with Param1/Param2`

### 日志里有什么？

```
(Global Middleware) SSR: false
(Mixin) /test2/val1/val2AsyncData: {"param1":"val1","param2":"val2"}
(Mixin) /test2/val1/val2Fetch: {"param1":"val1","param2":"val2"}
(LinksComponent) SSR: false [BeforeCreate]
(LinksComponent) SSR: false [Created] SampleProp: Test32, SampleData: Lorem Ipsum Data
(LinksComponent) Created Refs: 
(LinksComponent) SSR: false [Mounted] SampleProp: Test2, SampleData: Lorem Ipsum Data
(LinksComponent) Mounted Refs: Foo With No Params,Foo X With Param1,(...) 
```

### 刚刚发生了什么？

*   全局中间件在客户端处理。本地不是，因为它没有连接到此路由。
*   现在调用来自 *logRouteQueryAndParams* 的 Mixin 代码用于 fetch 和 asyncData。
*   LinksComponent 中的所有 Vue 生命周期挂钩都被调用

## 点击链接`Foo X with Param1`

### 日志里有什么？

```
(Global Middleware) SSR: false
(Mixin) /foo/x/val1AsyncData: {"id":"val1"}
(Mixin) /foo/x/val1Fetch: {"id":"val1"}
(Mixin) /foo/x/val1AsyncData: {"id":"val1"}
(Mixin) /foo/x/val1Fetch: {"id":"val1"}
(LinksComponent) SSR: false [BeforeCreate]
(LinksComponent) SSR: false [Created] SampleProp: SampleProp from Foo, SampleData: Lorem Ipsum Data
(LinksComponent) Created Refs: 
(LinksComponent) SSR: false [Mounted] SampleProp: SampleProp from Foo, SampleData: Lorem Ipsum Data
(LinksComponent) Mounted Refs: Foo With No Params,Foo X With Param1,(...) 
```

### 刚刚发生了什么？

*   全局中间件在客户端处理。
*   用于 fetch 和 asyncData 的来自 *logRouteQueryAndParams* 的 Mixin 代码现在被调用了两次！这是因为`foo.vue`和`foo/x/_id.vue`都包含 mixin，并且都是页面。实际上，父 route 和嵌套 route 中不会包含相同的 fetch(来自 mixin ),所以 fetch/asyncData 不会做同样的事情。
*   LinksComponent 中的所有 Vue 生命周期挂钩都被调用

## 点击链接`Foo Y with Param2`

### 日志里有什么？

```
(Global Middleware) SSR: false
(Mixin) /foo/y/val1AsyncData: {"id":"val1"}
(Mixin) /foo/y/val1Fetch: {"id":"val1"} 
```

### 刚刚发生了什么？

*   哦亲爱的！为什么这个输出与 Foo X 的如此不同？这是因为我们现在正在**嵌套路线**内导航。这个应用程序足够聪明，知道外壳(`foo.vue`)在`foo/x/val1`和`foo/y/val1`之间没有改变——改变的只是嵌套部分(`x/_id.vue`对`y/_id.vue`)。因此，没有必要重新生成任何与 foo.vue 相关的东西。我们只执行特定于 y/_id.vue 的东西——而且这个文件不包含单独的 LinksComponent，所以不运行它的生命周期方法。
*   全局中间件仍然在客户机上处理。
*   用于 fetch 和 asyncData 的来自 *logRouteQueryAndParams* 的 Mixin 代码现在被调用——但是只用于 foo/y/_id.vue

### 我们哪里出错了？

我们完全误解了/甚至没有阅读什么是嵌套组件，所以在某一点上我们有了一个类似于 foo route 中的结构，但是 foo.vue 页面没有包含`<router-view>`。路由工作得很好，但是提取只在路由改变时被调用，而不是参数改变时。例如，如果你从`/foo/x/1`到`/foo/x/2`——不会调用对 */foo/x/2* 的提取。但是如果你从`/foo/x/1`到`/test1`，然后到`/foo/x/2`，那么 fetch 被调用。

如果您处于类似的情况，并且由于某种原因，您实际上需要对 foo.vue 数据进行一些更改，那么您的最佳选择是添加对路线的监视，即:

```
watch: {
    '$route'(to, from) {
        // whatever you need to refresh goes here
        // you can get route (URL) params and query arguments before and after from `to` and `from` method parameters
    }
} 
```

# 自己打自己！

我希望上面的演示有意义——但是没有什么比拿这个示例项目自己玩更有启发性的了。添加钩子，扩展现有代码，浏览应用程序，观察会发生什么。如果有任何不清楚的地方，请告诉我！

在接下来的最后一部分，我将用一个简洁的表格来总结这两部分——敬请期待！

更新:[第三部分](https://dev.to/lilianaziolek/understanding-nuxt-vue-hooks-and-lifecycle-part-3-8fo)
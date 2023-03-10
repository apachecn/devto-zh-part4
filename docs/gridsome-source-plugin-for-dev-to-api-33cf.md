# 介绍戴夫。到 Gridsome 源插件

> 原文：<https://dev.to/perlatsp/gridsome-source-plugin-for-dev-to-api-33cf>

[![](img/c55545c690eb76cc74377237ea210d42.png)](https://www.npmjs.com/package/@perlatsp/gridsome-source-devto)

我刚刚为 Gridsome 创建了一个插件，这是一个使用 dev.to API 检索帖子的源代码插件。你可以在 NPM 注册表[这里](https://www.npmjs.com/package/@perlatsp/gridsome-source-devto)找到这个插件

## 什么是 Gridsome？

[![dev.to settins](img/ca2568aba292f927d028a8d2b3cdf1b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YFoFwaLK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://kociaj.com/content/uploads/2019/07/gridsome.gif)

好的，等一下！什么是 Gridsome？

很高兴你问了！

Gridsome 是一个类似于 Gatsby 的静态站点生成器(虽然还很新，所以功能不丰富),用于 [Vue](https://vuejs.org/) 框架。Gridsome 让开发者很容易构建现代的 JAMstack 网站&pwa，默认情况下速度很快。

## 为什么是 Gridsome？

如果您要使用 gridsome，默认情况下您有以下代码:

> *   **Local development with hot reloading** -check code changes in real time.
> *   **Data source plug-in** -Connect any popular headless CMS, API or Markdown-files.
> *   **File-based page routing** -Quickly create and manage routes with files.
> *   **Centralized data management** -pull data into the local unified GraphQL data layer.
> *   **vue.js for frontend** -a lightweight and approachable front-end framework.
> *   **Automatically optimized code** -Get code splitting and asset optimization out of the box.
> *   **Static file generation** -Secure deployment to any CDN or static web host.

有关 Gridsome 的更多深入信息，请阅读[文档](https://gridsome.org/docs)。

## 这个插件怎么用？

### 安装

要安装这个插件，你只需要把它作为一个依赖项添加到你的 gridsome 项目中。您可以通过使用`yarn`在终端上运行以下命令来实现。

```
yarn add @perlatsp/gridsome-source-devto 
```

或者如果你喜欢`npm`

```
npm install @perlatsp/gridsome-source-devto 
```

当安装完成时，您应该准备好下一步，即配置插件。

### 配置

在我们继续配置插件之前，我们首先需要做的是从 [https://dev.to](https://dev.to) 获取一个 API 密匙。为此，我们需要进入 dev.to 网站，进入[账户- >设置](https://dev.to/settings/account)，然后你应该会看到一个输入框，为令牌添加描述，并点击`Generate Token`按钮。完成此操作后，可能需要一段时间为您生成令牌(大约`200ms`,具体取决于您的互联网连接🤪).

[![dev.to settins](img/81c7e4da0c0148ac95562a02493f68ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T570fKEA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kociaj.com/content/uploads/2019/07/devtoapi.png)

#### 配置文件

当你得到你的令牌，我们可以继续下一步。将配置添加到`gridsome.config.js`文件。这个文件是大多数 gridsome 配置所在的地方。打开配置文件，将以下内容添加到`plugins`数组中。比如下面的

```
...other gridsome plugins
   {
      use: '@perlatsp/gridsome-source-devto',
      options: {
        typeName: 'Article',
        username:'DEVTO_USERNAME',
        apiKey: process.env.DEVTO_API_KEY,
        route: '/:slug',
      }
    }
... rest of config file 
```

我们在这里做的是告诉 gridsome 使用我们的插件`use: '@perlatsp/gridsome-source-devto'`和一个`options`对象。选项对象非常简单，我们正在分配一个`typeName: 'Article'`，这是我们的“帖子模型”的名称，我们稍后将使用它来查询帖子。

我们有`username:'DEVTO_USERNAME'`,这是我们想要从 API 中检索的作者的用户名。

#### ENV 文件

API 关键变量，`apiKey: process.env.DEVTO_API_KEY`，出于安全原因，它从`.env`文件中获取值，所以我们的代码库中没有这个。我们需要在项目根目录下创建一个`.env`文件，并添加下面的`DEVTO_API_KEY=THE_API_KEY_FROM_DEVTO_SITE`。我们需要做的最后一项配置是路由。这将是单个帖子的 URL 显示类型。更多关于 gridsome 路由[这里](https://gridsome.org/docs/routing/#routing)。

现在我们准备进入下一步，显示文章。

### 显示帖子

为了显示帖子，我们需要转到`Index.vue`文件并修改组件(如果组件不存在，创建一个)以反映以下内容:

```
<page-query>
query Home {
    allArticle {
      edges {
        node {
          id
          title
          published_at
          path
          description
          tag_list
          canonical_url
          cover_image
        }
      }
    }
  }
</page-query> 
```

这是一个 GraphQL 查询。

> GraphQL 是一种声明式查询语言，对于仅检索您所要求的数据特别有用。这又将导致更小的束。

我们正在注册名为`allArticle`(所有的**+我们在配置文件中注册的**类型名**)的实体。**

 **没有必要从我们的文章中获取所有数据，所以我们请求一些节点(字段),如 id、标题、描述等。

好了，我们有了循环查询。现在怎么办？就这样吗？

当然不是！现在我们需要以某种方式显示我们拥有的日期。为此，向上滚动到`组件。并对其进行修改以反映以下内容:

``html
<template>
<Layout>
<h1> Gridsome source plugin for
<img src="https://d2fltix0v2e0sb.cloudfront.net/dev-badge.svg" height="30"
width="30"></h1>
<div v-for="{ node } in $page.allArticle.edges" :key="node.id">
<h2> {{node.title}}</h2>
{{node.description}}
<div>
<g-link :to="node.path" class="single-post-link">Read More</g-link>
</div>
</div>
</Layout>
</template>`` 

 ``瞧，我们现在可以看到我们的帖子了！打开您的终端并运行`yarn develop`来编译并创建一个 dev 服务器。你的项目应该可以在 [http://localhost:8080](http://localhost:8080) 上找到。

如果你按照前面的步骤，你应该有类似下图的东西。顶部的导航，一个标题和显示标题，描述和文章链接的文章循环。

[![postsindex](img/8c2b0dc23ef7993415a5b5fc7645a1c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xHNmMy_t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kociaj.com/content/uploads/2019/07/posts.png)

我个人不喜欢它的视觉效果。让我们添加一些样式。

[![postscards](img/2a3d112db5a5e1dd7b5086969923531d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lqCshpYv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://kociaj.com/content/uploads/2019/07/postscards.png)

好一点了😄用简单的卡片。我们现在已经完成了项目，我们可以运行`yarn build`来生成所有的静态文件，并准备好部署！⛴

> 这个插件仍在开发中，需要做更多的工作。例如为单篇文章页面显示更多数据。在 [github](https://github.com/perlatsp/gridsome-source-devto) 上 PRs 更受欢迎``**
# 用网格分类法启动静态站点

> 原文：<https://dev.to/codegram/powering-up-static-sites-with-gridsome-taxonomies-3hoa>

*最初发表于 [Codegram 的博客](https://www.codegram.com/blog/powering-up-static-sites-with-gridsome-taxonomies)T3】*

在 Codegram，我们已经非常喜欢 jam stack(T2)，尤其是使用 Gridsome，一个 Vue.js 框架(对于 React devs，它相当于 Gatsby)。尽管在我写这篇文章的时候还只是版本`v0.6.9`，但它已经基本上可以投入生产了(除非你需要 [i18n](https://github.com/gridsome/gridsome/issues/23) ，并且提供了**出色的开发者体验**。我们用它建立了几个网站，最著名的是 [Full Stack Fest 的](https://2019.fullstackfest.com)和你正在阅读这篇文章的网站。

与简单的静态生成的 Nuxt 站点相比，Gridsome 最出色的地方是添加一个 **GraphQL 数据层**的能力，只需要几行代码，而且没有创建 API 的经验。我们网站的第一个也是最明显的需求就是博客。那么让我们来看看如何用 Gridsome 创建一个博客吧！

首先，我们需要安装几个插件:[@ grid some/source-file system](https://gridsome.org/plugins/@gridsome/source-filesystem)和[@ grid some/transformer-remark](https://gridsome.org/plugins/@gridsome/transformer-remark)。这将允许我们读取 markdown 文件并将它们转换成 GraphQL 集合。

因此，在`gridsome.config.js`文件中，我们添加了插件的配置:

```
module.exports = {
  plugins: [
    {
      use: '@gridsome/source-filesystem',
      options: {
        path: 'src/blog/*.md',
        typeName: 'BlogPost',
        route: '/blog/:slug'
      }
    }
  ],
  transformers: {
    remark: {
      // we can add options here
    }
  }
} 
```

然后，我们在`src/pages`中创建一个`blog.vue`文件，我们可以查询我们的 blogposts。就这么简单！

```
<template>
  <Layout>
    <BlogPost
      v-for="post in $page.blogPosts.edges"
      :key="post.node.id"
      :post="post"
    />
  </Layout>
</template>

<static-query>
  query BlogPost {
    blogPosts: allBlogPost(sortBy: "published_at", order: DESC) {
      edges {
        node {
          title
          path
          image(width:640, quality: 75)
        }
      }
    }
  }
</static-query> 
```

借助 GraphQL 的强大功能，我们只需要查询我们需要的字段。既然是主博客页面，我们不关心内容，所以我们只要求`title`、`path`和`image`(我们甚至可以要求具体的大小和质量！🤯).

每个帖子页呢？使用 plain Vue 或 Nuxt，我们需要创建一个带有`id`参数的路由，然后从`$route`对象中获取该参数，然后查询 post，将我们获取的数据设置为组件状态，在完成所有这些工作时，可能需要添加一个加载微调器…

[![](img/4c839c64db6b4f24a3e81061a7fab641.png)](https://i.giphy.com/media/njAjh98E1PUha/source.gif)

有了 Gridsome 就简单多了！我们只需要在`templates`文件夹中创建一个`BlogPost.vue`(记住`BlogPost`是我们在配置 app 时使用的`typeName`，并编写查询:

```
<template>
  <section>
    <h1>{{ $page.blogPost.title }}</h1>
    <!-- ... -->
  </section>
</template>

<page-query>
  query BlogPost ($path: String!) {
    blogPost: blogPost (path: $path) {
      title
      published_at
      author
      image(width:1024, quality: 75)
      content
    }
  }
</page-query> 
```

如您所见，该查询获得了一个我们传递的`$path`变量。但这一切都是为你做的，不需要得到`$route.params.id`或任何东西！

不过，这只是您可以使用 Gridsome 做的事情的开始。你可能会问，没有标签的博客有什么用？来补充一下吧！

我们需要另一个 GraphQL 集合，但是我们不想为每个标签创建一个文件，对吗？幸运的是有一个简单的方法！在`gridsome.config.js`中，我们可以添加对另一个`typeName`的引用。`create`选项将创建没有文件的收藏:

```
 {
      use: '@gridsome/source-filesystem',
      options: {
        path: 'src/blog/*.md',
        typeName: 'BlogPost',
        route: '/blog/:slug',
        refs: {
          typeName: 'Tag',
          route: '/blog/tag/:slug',
          create: true
        }
      }
    } 
```

现在我们可以用同样的方式查询标签来创建我们的标签列表:

```
<page-query>
  query Tags {
    tags: allTag {
      edges {
        node {
          id
          path
        }
      }
    }
  }
</page-query> 
```

但是我们需要为每个标签创建一个视图，列出带有该标签的文章，对吗？我们创建另一个模板`Tag.vue`，可以得到标签所属的帖子！

```
<page-query>
  query Tag($id: String!) {
    tag(id: $id) {
      id
      belongsTo(sortBy: "published_at", order: DESC) {
        edges {
          node {
            ... on BlogPost {
              title
              path
              image(width:640, quality: 75)
            }
          }
        }
      }
    }
  }
</page-query> 
```

*如果你不熟悉 GraphQL，`... on BlogPost`位可能看起来有点奇怪，它被称为[内联片段](https://graphql.org/learn/queries/#inline-fragments)。*

能够如此容易地建立关系是不是很棒？没有后端！为了更容易地访问帖子，我喜欢创建计算属性，这样代码更易读:

```
 computed: {
    posts() {
      return this.$page.tag.belongsTo.edges
    }
  } 
```

这可能感觉不到什么，但是这确实将静态站点带到了一个新的高度。我们可以在构成网站的所有部分之间建立复杂的关系。例如，我们在我们的一些[服务](https://www.codegram.com/services/)中添加了相关的帖子。怎么会？同时添加标签！

```
 // gridsome.config.js
    {
      use: '@gridsome/source-filesystem',
      options: {
        path: 'src/services/*.json',
        route: '/services/:id',
        typeName: 'Service',
        refs: {
          tags: {
            typeName: 'Tag',
            route: '/blog/tag/:slug'
          }
        }
      }
    }, 
```

因为我们已经为我们的博客文章创建了标签，所以没有必要再次添加`create: true`，它将匹配当前已有的标签。

在我们的服务查询中，我们添加了标签和它们的帖子:

```
<page-query>
  query Service($path: String!) {
    service(path: $path) {
      title
      description
      tags {
        id
        belongsTo(
          sortBy: "published_at",
          order: DESC,
          limit: 3,
          filter: {
            typeName: { eq: BlogPost }
          }) {
          edges {
            node {
              ... on BlogPost {
                title
                path
                image(width: 640, quality: 75)
              }
            }
          }
        }
      }
    }
  }
</page-query> 
```

*注意，由于现在标签同时引用了博客帖子和服务，我们需要通过`typeName`进行过滤，所以我们只得到帖子。*

很容易，每次我们添加关于 Vue 的帖子，它都会显示在 Vue 服务页面上。反之亦然！我们可以从帖子页面查询相关服务，你可以在这篇帖子的末尾看到。我们对案例研究也做了同样的事情！如果你读了一篇关于 [Decidim](https://www.codegram.com/blog/decidim-open-source-participatory-democracy-framework) 的帖子，你会看到一个案例研究的链接。张贴标签使我们能够显示整个网站的关系。

[![](img/8bdadf8d227a97254f9017c40e7a75b4.png)](https://i.giphy.com/media/XkDyB1jCZMxUs/source.gif)

<small>想象各种可能性</small>

正如我们所看到的，JAMStack 并不局限于只有几页的简单网站，或者最多是博客。我们可以非常容易地创建复杂的关系和过滤器，因此我们可以以多种方式(类别、系列、标签等)对我们的产品进行分类，添加相关产品，将它们链接到不同的内容类型…你能想到的！在这篇文章中，我们一直在使用文件加载器，但 Gridsome 支持多种来源:你可以使用流行的无头 CMS，如 [Contentful](https://gridsome.org/plugins/@gridsome/source-contentful) 或 [Netlify CMS](https://gridsome.org/plugins/gridsome-plugin-netlify-cms) 、 [Wordpress](https://gridsome.org/plugins/@gridsome/source-wordpress) ，甚至直接插入到 [MySQL 数据库](https://gridsome.org/plugins/gridsome-source-mysql)。所以即使你的网站有很多内容，用 JAMStack 也很容易管理。

显然，你甚至可以使用星球大战 API 作为资源。所以今天试试 Gridsome， *it **就是**你要找的框架。*

[![](img/242e34aa8f7ddbbfdfc3cd1552df3aec.png)](https://i.giphy.com/media/fZyssFynWmQSc/source.gif)
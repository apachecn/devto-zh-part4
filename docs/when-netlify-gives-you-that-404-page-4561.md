# 当 Netlify 给你 404 页的时候

> 原文：<https://dev.to/debs_obrien/when-netlify-gives-you-that-404-page-4561>

有没有在 Netlify 上部署过 Vue 或 Nuxt 应用，刷新时出现 404 页面？是的，这是怎么回事？

如果你使用 Vue 或者使用 Nuxt 作为单页应用程序，那么你的站点就是一个单页，所以你的页面的路径并不是一个真正的页面，比如说 mysite.com/blog.博客是你的页面，而是你的视图在渲染时被放到你的 index.html 中。博客实际上并不是以页面的形式存在的，它的魔力在于让单页应用程序看起来有很多页面，但实际上它们被称为单页应用程序，因为它们只有一个页面。

所以基本上，当有人刷新页面时，Netlify 在寻找那个叫做 blog 的页面，但找不到，因为它不是真正的页面。所以我们需要告诉 Netlify，如果你刷新或者有人没有先浏览主页就登陆了那个页面，基本上是把他们重定向到主页，这样 JavaScript 就可以发挥它的魔力，你想要的视图就可以呈现出来。

我们可以通过添加一个`_redirects`文件(是的，就像这样，_redirects，末尾没有任何内容)到我们的公共文件夹中，并使用一些重定向规则:
来轻松实现这一点

```
/*    /index.html   200
/*    /index.html   404 
```

Enter fullscreen mode Exit fullscreen mode

然后，如果你刷新页面或发送一个链接给某人，它将完美地工作。

如果你正在使用 Nuxt，有一个更简单的方法。nuxt 总有一个更简单的方法，但是如果你想的话，你仍然可以使用重定向文件，你只需要把它放在你的`static`文件夹中，把 index.html 改成`200.html`，因为这是 Nuxt 中的默认页面。

但是你可以在你的 nuxt.config 中创建一个[生成属性](https://nuxtjs.org/api/configuration-generate#fallback)，并添加值为`true`的键`fallback`，而不是必须这样做。这意味着，如果 Nuxt 处于 SPA 模式，当找不到页面时，可以回退到 200.html。200.html 是默认值，所以设置为真会给你这个，但你可以添加一个 200.html 字符串，如果你想的话。

```
export default {
  generate: {
    fallback: true
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

有时候你会遇到这样的情况，你有一个 Nuxt 静态生成的站点，你不能理解为什么有些页面可以刷新，而有些却不行。是的，添加回退到 true 会解决这个问题，但是你可能不知道为什么它在一些上有效而在另一些上无效。让我解释一下 Nuxt 静态站点是如何工作的。

Nuxt 将为 pages 文件夹中的每个`.vue`文件生成一个页面。所有这些页面都是自动静态页面。然而，有时我们有动态内容，如来自博客的内容。我们创建了动态页面，但是 nuxt 不能为每个博客文章创建一个页面，因为它不知道这些页面的名称是什么。(你的网站仍然可以工作，因为 Nuxt 会巧妙地将这些页面变成一个单页应用程序，而不是静态页面)。因此，为了生成这些静态页面，我们需要使用[生成属性](https://nuxtjs.org/api/configuration-generate#routes)，我们只需告诉 Nuxt 要生成什么路由，例如:

```
export default {
  generate: {
    routes: [
      '/blogpost/1',
      '/blogpost/2',
      '/blogpost/3'
    ]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，每次我们添加一篇博客文章时，都必须将路线放在那里，这将是一件痛苦的事情，所以在这种情况下，我们应该做的是编写一个映射所有博客文章的函数:

```
generate: {
    routes: function () {
      return axios.get('https://my-api/blogposts')
      .then((res) => {
        return res.data.map((post) => {
          return '/blogposts/' + post.id
        })
      })
    } 
```

Enter fullscreen mode Exit fullscreen mode

这将为每篇博客文章生成一个页面，我们可以通过使用 generate 命令并读取终端中打印的内容来进行测试。然后，您将看到哪些帖子已经生成，哪些没有生成。您也可以在 Netlify 部署日志中检查这一点。

如果您想了解更多关于如何使用 Nuxt 部署到 Netlify 的信息，请参阅 [Nuxt 文档](https://nuxtjs.org/faq/netlify-deployment#how-to-deploy-on-netlify-)

还可以查看 Vue 学校关于如何部署的[免费视频](https://vueschool.io/lessons/how-to-deploy-nuxtjs-to-netlify?friend=nuxt)
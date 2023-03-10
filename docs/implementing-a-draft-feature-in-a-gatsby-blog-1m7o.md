# 在 Gatsby 博客中实现一个功能草案

> 原文：<https://dev.to/cole_ruche/implementing-a-draft-feature-in-a-gatsby-blog-1m7o>

你好，盖茨比博客的主人！

最近，我发现自己在思考和写作盖茨比。主要是因为，就像 Bootstrap 和 React 一样，这是我开始学习前端开发以来遇到的最好的事情之一。现在我要分享一些我学到的东西(并不新鲜)。

当我开始与盖茨比一起建立我的作品集兼博客网站并开始写作时，我遇到了一个问题。对于一个也在 [Dev.to](https://dev.to) 上写文章的人来说——你可以在那里开始写一篇文章，然后*起草*然后开始写一篇新的——我有点失望为什么 uptil now，Gatsby 的[博客启动器](https://www.gatsbyjs.org/starters/gatsbyjs/gatsby-starter-blog/)不包括保存草稿和只发布你设置为“已发布”的帖子的内置功能，正如在 Dev.to 上看到的那样

> 我在网上找到了其他替代方案。本文末尾将列出它们的链接。此外，本文假设你正在运行一个由盖茨比的`gatsby-starter-blog`驱动的博客

关于如何解决这个问题，我的第一个想法是从 Markdown 文件中寻找处理页面创建的代码块，我在`gatsby-node.js` :
中找到了这个

```
exports.createPages = async ({ graphql, actions }) => {
  const { createPage } = actions

  const blogPost = path.resolve(`./src/templates/blog-post.js`)
  const result = await graphql(
    `
      {
        allMarkdownRemark(
          sort: { fields: [frontmatter___date], order: DESC }
          limit: 1000
        ) {
          edges {
            node {
              fields {
                slug
              }
              frontmatter {
                title
              }
            }
          }
        }
      }
    `
  )

  if (result.errors) {
    throw result.errors
  }

  // Create blog posts pages.
  const posts = result.data.allMarkdownRemark.edges

  posts.forEach((post, index) => {
    const previous = index === posts.length - 1 ? null : posts[index + 1].node
    const next = index === 0 ? null : posts[index - 1].node

    createPage({
      path: post.node.fields.slug,
      component: blogPost,
      context: {
        slug: post.node.fields.slug,
        previous,
        next,
      },
    })
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所猜测的，这些页面是从使用`allMarkdownRemark`查询获得的数据中创建的。这是我们施展魔法的地方。

就在`sort`命令旁边，我们可以添加自己的`filter`规则，只获取我们标记为已发布的帖子。为此，您应该在文章的 frontmatter 中添加一个变量`published`，根据文章的状态将其设置为`true`或`false`。例如，要将一篇文章设置为草稿(即未提交)，请将其添加到文件的首页:`published: true`。

现在我们有了一种方法来标记文章是否准备好发布，我们回到 GraphQL 查询，并像这样修改它:

```
exports.createPages = async ({ graphql, actions }) => {
  const { createPage } = actions

  const blogPost = path.resolve(`./src/templates/blog-post.js`)
  const result = await graphql(
    `
      {
        allMarkdownRemark(sort: {fields: frontmatter___date, order: DESC}, filter: {frontmatter: { published: {eq: true} }}, limit: 1000)
        ...
        // your other code
        ...

        slug: post.node.fields.slug,
        previous,
        next,
      },
    })
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

这一改变确保 Gatsby 只过滤掉我们在 it 's fromtmatter 中将其发布变量设置为`true`的帖子。请注意把这个规则添加到你处理文章的任何地方，比如在你的`src/pages/index.js`文件中，那里有一个列出你文章的类似查询，如果你用`gatsby-plugin-feed`创建你的 RSS 源也是如此。

正如我在开始之前所说的，在网络上有其他的选择。看看这个由 [Janosh](https://www.google.com.ng/url?sa=t&source=web&rct=j&url=https://janosh.io/blog/exclude-drafts-from-production/&ved=2ahUKEwi99uvmz63kAhVRQxUIHbWLAJ4QFjAAegQIAxAB&usg=AOvVaw3jun-nNSBWsJ8Gqq71dGYi) 提出的方法和这个由 [Chase Adams](https://chaseonsoftware.com/gatsby-drafts/#how-i-write-drafts-in-gatsby) 提出的方法。使用你喜欢的任何方法，如果你有自己的超级酷的方法，请在评论区与我们分享一些代码片段，或者粘贴文章的链接。
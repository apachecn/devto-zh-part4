# 使用 Gatsby 主题选项

> 原文：<https://dev.to/hagnerd/using-gatsby-theme-options-16bm>

这是《盖茨比主题入门》系列文章的第二篇，我们将逐步了解主题。在第一篇博文中，我们从头开始建立了一个 Gatsby 网站，并添加了`gatsby-theme-blog`，它将一些 Markdown 或 Mdx 内容放在指定的文件夹中，将它们转换为我们网站上的页面，并提供了一些不错的基础组件和样式。

如果你还没有使用你的第一个盖茨比主题来阅读[，你可能想在继续之前这样做。](https://dev.to/hagnerd/using-your-first-gatsby-theme-hep)

在本帖中，我们将深入探讨`gatsby-theme-blog`给我们定制体验的选项。

在以后的文章中，我们将讨论什么是隐藏，以及如何隐藏组件、样式和数据。

* * *

## 有哪些选项？

如果我们看一下`gatsby-theme-blog`包的[文档](https://www.npmjs.com/package/gatsby-theme-blog)，有四个选项可供我们选择。

1.  `basePath`默认为“/”。它是博客索引页面的 url，也是所有博客文章的根 url。
2.  `contentPath`默认为`/content/posts`。它是。md/。mdx 文件转换成博客文章。
3.  `assetPath`默认为`/content/assets`。这是您的生物组件头像(图片)的位置。
4.  `mdx`默认为真。这决定了`gatsby-theme-blog`是应该为你配置`mdx`，还是你自己处理。如果您已经为其他页面安装并配置了`gatsby-mdx`，您可以将此设置为 false。

* * *

## 我们如何覆盖它们？

我们可以在 Gatsby 网站的`gatsby-config.js`中覆盖这些默认值。当你不需要改变主题中的任何选项时，你的配置可能看起来像这样:

```
// gatsby-config.js
module.exports = {
  plugins: [
    'gatsby-theme-blog'
  ]
} 
```

如果我们想改变一个或多个选项，我们将使用插件/主题的手写语法。

```
// gatsby-config.js
module.exports = {
  plugins: [
    {
      resolve: 'gatsby-theme-blog',
      options: {
        basePath: '/blog'
      }
    }
  ]
} 
```

您可能需要查阅您正在使用的主题的文档，看看是否有您必须设置的选项，或者是否所有选项都是可选的。

* * *

## 选项用在哪里？

如果我们进入 Gatbsy 网站的文件夹，在我们最喜欢的编辑器中打开`node_modules/gatsby-theme-blog/gatsby-config.js`,我们会看到类似这样的内容。

```
// node_modules/gatsby-theme-blog/gatsby-config.js
module.exports = options => {
  const { mdx = true } = options

  return {
    siteMetadata: {
      title: `Blog Title Placeholder`,
      author: `Name Placeholder`,
      description: `Description placeholder`,
      social: [
        {
          name: `twitter`,
          url: `https://twitter.com/gatsbyjs`,
        },
        {
          name: `github`,
          url: `https://github.com/gatsbyjs`,
        },
      ],
    },
    plugins: [
      mdx && {
        resolve: `gatsby-plugin-mdx`,
        options: {
          extensions: [`.mdx`, `.md`],
          gatsbyRemarkPlugins: [
            {
              resolve: `gatsby-remark-images`,
              options: {
                // should this be configurable by the end-user?
                maxWidth: 1380,
                linkImagesToOriginal: false,
              },
            },
            { resolve: `gatsby-remark-copy-linked-files` },
            { resolve: `gatsby-remark-numbered-footnotes` },
            { resolve: `gatsby-remark-smartypants` },
          ],
          remarkPlugins: [require(`remark-slug`)],
        },
      },
      {
        resolve: `gatsby-source-filesystem`,
        options: {
          path: options.contentPath || `content/posts`,
          name: options.contentPath || `content/posts`,
        },
      },
      {
        resolve: `gatsby-source-filesystem`,
        options: {
          path: options.assetPath || `content/assets`,
          name: options.assetPath || `content/assets`,
        },
      },
    // ...more config
  }
} 
```

需要注意的最重要的一点是，`gatsby-config.js`文件是一个
函数，它接受一个对象(这里称为选项)作为参数，并返回一个配置对象。如果我们仔细观察，我们会看到`gatsby-theme-blog`的四个选项中的三个在配置中的不同位置使用。

`mdx`用来决定是否为我们配置`gatsby-mdx`，还有一些对 Markdown/Mdx 有用的插件。

`contentPath`在`gatsby-source-filesystem`配置
中使用，决定在我们的文件系统中的什么地方寻找帖子，以及当我们用 GraphQL 查询它们时该如何称呼它们。

`assetPath`也在`gatsby-source-filesystem`配置中使用，但是这次决定在我们的文件系统中的什么地方寻找我们的资产，以及当我们用 GraphQL 查询它们时该如何称呼它们。

所以我们找到了四个选项中的三个。第四个在哪里用？

为此，我们将深入了解`node_modules/gatsby-theme-blog/gatsby-node.js`。

```
// node_modules/gatsby-theme-blog/gatsby-node.js
// Ensure that content directories exist at site-level
exports.onPreBootstrap = ({ store }, themeOptions) => {
  const { program } = store.getState()

  basePath = themeOptions.basePath || `/`
  contentPath = themeOptions.contentPath || `content/posts`
  assetPath = themeOptions.assetPath || `content/assets`

  const dirs = [
    path.join(program.directory, contentPath),
    path.join(program.directory, assetPath),
  ]

  dirs.forEach(dir => {
    debug(`Initializing ${dir} directory`)
    if (!fs.existsSync(dir)) {
      mkdirp.sync(dir)
    }
  })
} 
```

你不需要理解整个文件中发生了什么，事实上这就是 Gatsby、Gatsby 插件和现在的 Gatsby 主题的美妙之处，但是如果我们看一下`onPreBootstrap`函数，我们可以看到第二个参数是一个对象，它具有与`gatsby-config.js`文件中相同的可用选项。

当具有该主题的 Gatsby 网站启动时，Gatsby 将检查这些路径(`contentPath`和`assetPath`)是否存在。如果它们不存在，Gatsby 将使用`mkdirp`包来确保它们被创建。

我们需要查看的下一个地方是同一个文件中的`createPages`函数。这一次，我们利用了在`onPreBootstrap`函数中设置的全局`basePath`变量。相关部分在这里:

```
// node_modules/gatsby-theme-blog/gatsby-node.js
// inside of the exports.createPages function
  createPage({
    path: basePath,
    component: PostsTemplate,
    context: {
      posts,
      siteTitle,
      socialLinks,
    },
  }) 
```

需要理解的重要一点是，无论我们的`basePath`是什么，它都将是我们的`PostsTemplate`通过上下文对象传递数据的路径。

* * *

需要理解的内容很多，但让我们快速回顾一下:

*   主题文档*应该*列出可用的选项，以及默认选项
*   如果需要的话，可以使用 Gatsby 中标准的手写插件语法来覆盖主题默认值
*   您还可以查看主题的`gatsby-config.js`和`gatsby-node.js`以了解更多关于如何/在哪里使用这些选项的上下文，如果文档还有更多需要改进的地方。

我希望这篇文章是有帮助的。下一次我们将深入阴影，回顾阴影在《盖茨比》中的概念。
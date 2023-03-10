# 盖茨比与 WPGraphQL，ACF 和 Gatbsy-Image

> 原文：<https://dev.to/nevernull/gatsby-with-wpgraphql-acf-and-gatbsy-image-72m>

所以，围绕着盖茨比，WordPress 和 WPGraphQL 发生了很多事情。有时，它会让人感到困惑，许多给定的功能很难找到或弄清楚。我现在正在做一个客户项目，不知何故不得不**处理图片**，这些图片是我通过我的 WordPress 后端获取的。

在尝试了各种各样的解决方案并通过 Gatsby 的插件库工作后，我最终找到了一个小文档，它引导我找到了一个适合我的解决方案。而且，因为一些在 WPGraphQL Slack 聊天中非常有帮助的人要求我写下这个解决方案...我来了。

> 我只是想把我的 WordPress 媒体作为静态文件放在我的 Gatsby 应用程序中，并使用 gatsby-image 插件进行转换/优化。

## 使用的库

*   [盖茨比](https://www.gatsbyjs.org/)
    *   [盖茨比来源图 ql](https://www.gatsbyjs.org/packages/gatsby-source-graphql)
    *   [盖茨比形象](https://www.gatsbyjs.org/packages/gatsby-image/)
        *   [盖茨比源文件系统](https://www.gatsbyjs.org/packages/gatsby-source-filesystem/)
        *   [盖茨比-变形金刚-夏普](https://www.gatsbyjs.org/packages/gatsby-transformer-sharp)
        *   [盖茨比-外挂-夏普](https://www.gatsbyjs.org/packages/gatsby-plugin-sharp/)
*   WordPress
    *   [ACF](https://www.advancedcustomfields.com/)
    *   [WPGraphQL](https://github.com/wp-graphql/wp-graphql) | [文档](https://docs.wpgraphql.com/)
    *   [WPGraphQL ACF](https://github.com/wp-graphql/wp-graphql-acf)

* * *

## 盖茨比配置

```
/* --------- gatsby-config.js --------- */

module.exports = {

  plugins: [

    {
      resolve: `gatsby-source-filesystem`,
      options: {
        name: `images`,
        path: `${__dirname}/src/assets/images`,
      },
    },
    `gatsby-transformer-sharp`,
    `gatsby-plugin-sharp`,
    {
      resolve: "gatsby-source-graphql",
      options: {
        typeName: "WPGraphQL",
        fieldName: "wpgraphql",
        url: `https://${YOUR_DOMAIN}/graphql`,
      },
    },
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

*   确保所有的插件都在你的配置文件中被正确的提及
*   出于某种原因，如果在配置中没有使用`gatsby-source-filesystem`，可能会导致错误。确保它解析为有效的路径，即使文件夹中没有图像。

* * *

## 盖茨比节点

```
 /* --------- gatsby-node.js --------- */

const { createRemoteFileNode } = require(`gatsby-source-filesystem`)

exports.createResolvers = async (
  {
    actions,
    cache,
    createNodeId,
    createResolvers,
    store,
    reporter,
  },
) => {
  const { createNode } = actions

  await createResolvers({
    WPGraphQL_MediaItem: {
      imageFile: {
        type: "File",
        async resolve(source) {
          let sourceUrl = source.sourceUrl

          if (source.mediaItemUrl !== undefined) {
            sourceUrl = source.mediaItemUrl
          }

          return await createRemoteFileNode({
            url: encodeURI(sourceUrl),
            store,
            cache,
            createNode,
            createNodeId,
            reporter,
          })
        },
      },
    },
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

这才是你真正需要的。让我们稍微分解一下:

*   这取决于你的配置。它从你的 gatsby-source-graphql 的`typeName`开始。
*   createRemoteFileNode 让您能够获取远程文件，并自动将它们添加到您的模式中。
*   `imageFile`:将是您可以查询的类型(见下图)。
*   将把 MediaItems 添加为文件，这很好，因为现在 gatsby-image 可以使用它了。
*   这里我们对来自 WordPress 的 Url 进行编码，以确保它被正确解析，即使图像路径包含元音字母。

## 页面查询

```
 # page.js  query  {  WPGraphQL  {  pages  {  nodes  {  featuredImage  {  sourceUrl  imageFile  {  childImageSharp  {  fixed  {  ...GatsbyImageSharpFixed  }  }  }  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

**重要:**总是查询`sourceUrl`，否则不起作用，因为解析器会错过那些数据。

## 最后的想法

这就是全部了。它也适用于 ACF 媒体文件。一样一样。

几乎...我认为值得一提的是，通过这种方法，所有来自 WordPress 的可用媒体文件都将被提取到缓存中。因此，如果出于某种原因，缓存发生了清除，我想它需要再次将它们拉入。为了提高性能，从 Gatbsy 缓存中取出所有媒体文件可能更有用。这意味着，您必须自己做创建模式的艰苦工作。我相信也会有很好的解决方案。如果有人有这方面的经验并分享他们的想法，我会很高兴:)

## 缓存

如果你的服务器不支持 etags，`gatsby-source-filsystem`的默认缓存将不起作用。

你可以在这里查看罗伯特·马歇尔的定制解决方案，它应该会稍微快一些，并且不依赖于 etags:[https://thoughtsandstuff . com/Gatsby-with-WordPress-caching-downloaded-media-images-to-reduce-build-time/](https://thoughtsandstuff.com/gatsby-with-wordpress-caching-downloaded-media-images-to-reduce-build-time/)

## 即将到来

我计划写一篇关于为一个客户端就绪项目设置 Gatbsy + WordPress + ACF + Polylang 的大文章，该项目通过 ACF 灵活的内容域提供多语言支持和动态内容创建。我想知道你是否会感兴趣。任何想法和建议都非常感谢。

## 参考文献

请看这里的盖茨比文档:[https://www . gatsbyjs . org/docs/schema-customization/# feeding-remote-images-into-Gatsby-image](https://www.gatsbyjs.org/docs/schema-customization/#feeding-remote-images-into-gatsby-image)
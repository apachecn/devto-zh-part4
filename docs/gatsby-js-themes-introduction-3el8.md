# 盖茨比. js 主题介绍

> 原文：<https://dev.to/changoman/gatsby-js-themes-introduction-3el8>

*本文最初发表于[codebushi.com](https://codebushi.com/gatsbyjs-themes-introduction/)T3】*

[盖茨比主题文档演示](https://gatsby-theme-document.netlify.com/) <small>( [查看来源](https://github.com/codebushi/gatsby-theme-document-example) )</small>

做一个 Gatsby.js 站点，主要有两种方式开始。你可以通过安装`gatsby` `react`和`react-dom`从零开始。然后，根据您的需求，您可以为您的项目添加各种 Gatsby 插件和其他 npm 包。

另一个更快的方法是使用盖茨比启动器。Gatsby Starter 本质上是针对特定类型的站点或应用程序的样板代码和 npm 包。例如，如果你想创建一个博客，有一个 [Gatsby Starter 博客](%5Bhttps://github.com/gatsbyjs/gatsby-starter-blog%5D(https://github.com/gatsbyjs/gatsby-starter-blog))，里面有你需要的所有插件和包，已经在`package.json`和`gatsby-config.js`文件中配置好了。你所要做的就是克隆启动程序，进行修改，并使用 markdown 文件撰写文章。

Gatsby Starter 的问题在于，一旦用户克隆并更改了初始代码，它就变成了自己的实体。新的代码库是从最初的起始代码中分出来的。如果 starter 有一个 bug 修复或新特性，没有好的方法来为 starter 的现有用户更新代码。

盖茨比主题就是为了解决这个问题而产生的。盖茨比主题本质上是一个已经被制作成 npm 包的开端。它可以像任何其他包一样容易地更新，并且像在`gatsby-config.js`文件中的 Gatsby 插件一样使用。让我们尝试使用主题[盖茨比主题文档](https://github.com/codebushi/gatsby-theme-document)。

## 安装盖茨比主题

使用 Gatsby CLI

```
gatsby new document-site https://github.com/codebushi/gatsby-theme-document-example
cd document-site
gatsby develop 
```

网站应该在 [http://localhost:8000/](http://localhost:8000/) 首先，让我们看看`gatsby-config.js`文件。

```
module.exports = {
  siteMetadata: {
    title: `Document by Code Bushi`,
    name: `Code Bushi`,
    siteUrl: `https://gatsby-theme-document.netlify.com`,
    description: `This is my description that will be used in the meta tags and important for search results`,
    social: [
      {
        name: `github`,
        url: `https://github.com/codebushi/gatsby-theme-document`
      },
      {
        name: `twitter`,
        url: `https://twitter.com/HuntaroSan`
      }
    ],
    sidebarConfig: {
      forcedNavOrder: ['/introduction', '/codeblock'],
      ignoreIndex: true
    }
  },
  plugins: [{ resolve: `gatsby-theme-document` }]
}; 
```

注意在最底部，`plugins`数组包含主题`gatsby-theme-document`。这就是我们如何告诉盖茨比使用一个特定的主题。如果一个主题有额外的选项，你也可以在这里添加。在这种情况下，主题的选项在`siteMetadata`中，像社交媒体链接和`sidebarConfig`。

需要注意的是，这个存储库并不是实际主题的原始回购。这是一个已经在`package.json`和`gatsby-config.js`中预先配置了`gatsby-theme-document`的示例回购。如果我们打开`/src`目录，我们可以看到这里已经有一些文件夹和文件。示例 repo 也提供了这些信息，因此我们知道哪些文件应该被“屏蔽”以正确使用主题。

### 盖茨比主题中的成分遮蔽

使用 Gatsby 主题，原始主题的`/src`目录中的文件可以被使用该主题的 Gatsby 站点隐藏或覆盖。如果你以前在 Wordpress 中使用过“子主题”,这是一个类似的概念。

查看我们安装的主题示例站点，我们可以在`/src/gatsby-theme-document/logo.mdx`中看到一个徽标文件。我们对这个文件所做的任何更改都将覆盖主题中的原始徽标文件，该文件可以在`node_modules/gatsby-theme-document/src/logo.mdx`找到。

这就是我们如何定制一个 Gatsby 主题，并且在将来仍然可以获得 bug 修复或新特性。打开`/src/gatsby-theme-document/logo.mdx`中的徽标文件并进行更改

```
<!-- Document -->

YOUR LOGO 
```

您可能需要刷新页面，但是您站点的徽标现在应该更新了！

事实上，`node_modules/gatsby-theme-document/src`目录中的任何文件都可以在我们当前的站点中被复制和“隐藏”。如果你想彻底改变`Header`或`Layout`，只需将这些组件从`node_modules/gatsby-theme-document/src/components`文件夹中复制到你的`/src/gatsby-theme-document/components`文件夹中，对其进行阴影处理。

接下来，我们可以看看`/src/gatsby-plugin-theme-ui/colors.js`中的`colors.js`文件。这个特殊的主题是使用主题 UI 构建的，这个`colors.js`文件对应于主题 UI 中的[颜色模式特性](%5Bhttps://theme-ui.com/color-modes%5D(https://theme-ui.com/color-modes))。这是原始主题的另一个阴影文件，所以我们可以通过编辑这个文件来覆盖原始颜色。尝试将黑暗模式的`background`颜色改为`#000`

```
modes: {
    dark: {
      text: "#fff",
      background: "#000",
      primary: "#f638dc",
      secondary: "#ff7976",
      sidebar: "#101d3c",
      borderColor: "rgba(255, 255, 255, 0.15)"
    }, 
```

如果你回到这个网站，你会发现发生了变化。您可以编辑或添加额外的颜色模式到这个`colors.js`文件，主题将自动识别这些变化。网站标题右上角的颜色切换图标可以让你在不同的颜色模式之间切换。

### 用 MDX 书写内容

既然我们已经了解了如何定制我们的主题，我们实际上可以开始向站点添加内容了。如果我们查看`/content`下的示例内容文件，您会注意到这些是`.mdx`文件。该主题使用 MDX 而不是传统的 markdown 文件，这允许您在内容文件中包含 React 组件以及编写 JSX。你可以从他们的[网站](https://mdxjs.com/)上了解更多关于 MDX 的好处。

您可以简单地编辑或添加到`/content`文件夹中的现有 mdx 文件来填充站点上的页面。因为这些页面是由 Gatsby 动态创建的，所以您可能需要重新加载页面才能看到新页面出现在导航中。

要手动设置哪些页面以什么顺序出现在导航中，您可以打开`gatsby-config.js`文件并编辑`forcedNavOrder`数组设置。

```
sidebarConfig: {
  forcedNavOrder: ["/introduction", "/codeblock"],
  ignoreIndex: true
} 
```

这就结束了盖茨比主题的快速介绍。希望这篇文章已经启发了你尝试为你的下一个 Gatsby 站点使用一个主题，并且给了你一些关于如何定制主题的见解。每个主题都是以不同的方式构建的，所以请阅读您正在使用的主题的文档，以便正确设置它。

[盖茨比主题文档演示](https://gatsby-theme-document.netlify.com/) <small>( [查看来源](https://github.com/codebushi/gatsby-theme-document-example) )</small>
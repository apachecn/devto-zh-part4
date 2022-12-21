# 开始使用 Gatsby

> 原文：<https://dev.to/teekatwo/getting-started-using-gatsby-c6e>

我对盖茨比已经好奇了一段时间。我听说过**JAM**STACK(**J**avaScript， **A** PIs，和预渲染 **M** arkup)以及静态站点生成器有一段时间了。我在 egghead.io 上保存了一门盖茨比课程，有一天我决定，我正在构建的一个副业项目可能会很适合使用它。所以，现在是开始的最佳时机。

### 视频教程是很好的起点

我个人是视频教程的粉丝，我是一个视觉学习者，我喜欢看人们建造东西。我看着[用 React 和 Markdown](https://egghead.io/courses/build-a-blog-with-react-and-markdown-using-gatsby) 建了一个博客，惊讶于这一切看起来是如此的直接和简单。大约一年前用过盖茨比，但没有 React 的经验。我对任何东西是如何工作的都相当困惑。

现在我已经接受了反应训练，我想是时候再试一次了。在看了 Egghead 系列和 Youtube 系列之后，为了更好地理解如何使用 Gatsby，我认为是时候开始了。在阅读了关于人们应该如何尝试和维护自己的博客的文章后，这个决定就很明显了。做博客！这将是一个完美的方式来控制盖茨比的感觉，并做出一些对我有益的事情。

### 使用 Gatsby CLI 开发一个站点

这是我遇到的最简单的设置之一。

安装 CLI
创建新站点
进入新创建的站点文件夹
开发 Gatsby 站点。

**技术指导:**
在你的终端上运行`npm install -g gatsby-cli`来安装 CLI。然后运行`gatsby new gatsby-site`，将“gatsby-site”替换为您想要的文件夹名称。创建文件夹后，将目录更改为新创建的文件夹。然后你可以运行`gatsby develop`，Gatsby 将启动一个开发服务器，它可以热加载，并可以在`localhost:8000`访问。

遵循这些说明后，您的 Gatsby 站点就启动并运行了。如果你已经为一个基本的 React 应用程序做过`create-react-app`,你会感觉很熟悉。Gatsby 的设置方式与 React 应用程序非常不同，需要特殊处理。

在 Gatsby 中，有两个重要的文件控制着站点的各个方面。我要描述的两个是`gatsby-config.js`和`gatsby-node.js`。与普通的 React 站点不同，这两个文件是站点的心脏和灵魂。配置将控制你对文件和插件的访问，节点将控制页面的创建，等等。

### 盖茨比-config.js

这是你的站点配置，为你的站点准备的*惊喜*。

我在这个页面上使用的选项是站点元数据和插件。还有几个我还没有用到的，[配置选项](https://www.gatsbyjs.org/docs/gatsby-config/#configuration-options)

#### 站点元数据

```
 siteMetadata: {
    title: `Tori Pugh`,
    description: `Kick off your next, great Gatsby project with this default starter. This barebones starter ships with the main Gatsby configuration files you might need.`,
    author: `@gatsbyjs`,
    menuLinks: [
      {
        name: 'Work',
        link: '/work'
      },
      {
        name: 'About',
        link: '/about'
      },
      {
        name: 'Contact',
        link: '/contact'
      },
      {
        name: 'Blog',
        link: '/blog'
      }
    ]
  } 
```

这是一个对象，它将包含您希望保持一致的站点范围的数据。这可能是网站名称、菜单链接或您在页面间使用的任何内容。

#### 插件

```
plugins: [
    `gatsby-transformer-json`,
    `gatsby-plugin-react-helmet`,
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        name: `project`,
        path: `${__dirname}/src/data`
      }
    },
  ] 
```

插件可以只按名称列出，也可以带选项。如果它接受选项，它将需要是对象形式，resolve 是列出的名称，后跟一个 options 对象。

### 盖茨比-node.js

这是期望使用 Gatsby 节点 API 的地方。我学到的文件中最重要的部分是在构建过程中用`createPages`创建新页面。

CreatePages 的工作原理是获取一个 GraphQL 模式，并使用它来引用和查询一些数据。可以在函数中使用查询的数据来创建页面。

```
 workPages: allMarkdownRemark(sort: {fields: frontmatter___role}, filter: {frontmatter: {client: {regex: ""}}}) {
    edges {
      node {
        frontmatter {
          title
          path
          client
          github
          draft
          description
          deliverable
          role
          project_description
          url
        }
        html
      }
    }
  } 
```

这个 GraphQL 查询找到我所有的工作项目，并将它们以数组的形式返回。然后，我将获取这个数组，遍历它，并为数组中的每一项创建页面。

```
 res.data.workPages.edges.forEach(({ node }) => {
      createPage({
        path: node.frontmatter.path,
        component: workTemplate,
      })
    }) 
```

资源:

[盖茨比节点 API](https://www.gatsbyjs.org/docs/node-apis/)

[Gatsby 教程第七部分——从数据中以编程方式创建页面](https://www.gatsbyjs.org/tutorial/part-seven/)

GatsbyJS 教程#5 -用 Markdown 建立博客第 2 部分 -应该看第 1 部分来理解这一部分的基础。

### 页面文件夹

Gatsby 将自动为在`src/pages`文件夹中创建的任何 React 组件创建一个页面。设置后，初始页面应该是- index.js，page-2.js，404.js。如果您更改这些文件中的任何内容，它将对网站上的相应页面进行更改，如果您添加新的 React 组件，将会自动为其生成一个页面。

示例新页面。用下面的代码创建一个新文件`src/pages/about.js`。

```
 import React from "react"
  const AboutPage = () => (
    <main>
      <h1>About the Author</h1>
      <p>Welcome to my Gatsby site.</p>
    </main>
  )
  export default AboutPage 
```

像上面这样简单的代码就可以在《盖茨比》中产生新的一页。然后你可以去`localhost:8000/about`查看你的新页面。

[自动创建页面](https://www.gatsbyjs.org/docs/recipes/#creating-pages-automatically)

## 你从这里去了哪里

这只是让 Gatsby 网站在本地运行的最低要求。有了下一步做什么的想法，自动或动态地创建新页面；用 GraphQL 以某种方式使用数据；或者使用不同的插件和选项来改变你的网站/应用程序的功能，你可以做任何你想做的事情。

我将记录我创建一个工作博客的更多步骤，然后将这个博客转变成一个完整的网站。这决不是详尽无遗的，因为我自己并不知道关于盖茨比的一切。我只是分享一下我在盖茨比经历中所做的事情。

## 资源

Gatsby 网站上有一些我见过的最好的文档。很多时候，我不知道如何去做一些事情，我只是跟着他们的教程或者阅读他们大量的文档，最后我可以把它整理出来。

[盖茨比文档](https://www.gatsbyjs.org/docs/)
[盖茨比参考指南](https://www.gatsbyjs.org/docs/guides/)
[盖茨比 API 参考](https://www.gatsbyjs.org/docs/api-reference/)
[盖茨比食谱](https://www.gatsbyjs.org/docs/recipes/)
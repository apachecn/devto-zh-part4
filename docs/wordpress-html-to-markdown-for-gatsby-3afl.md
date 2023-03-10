# Wordpress HTML 为盖茨比降价

> 原文：<https://dev.to/shubho/wordpress-html-to-markdown-for-gatsby-3afl>

我目前正在用 WordPress 作为后端，Gatsby 作为前端创建我的博客。《盖茨比》最吸引人的特点之一就是插件。几乎所有你想在博客上使用的功能都可以作为插件获得，或者你也可以自己创建一个。作为一个涉猎过 WordPress 插件(但不精通 PHP)并了解 JavaScript 的开发人员，我觉得为 Gatsby 创建插件要容易得多。当然，这是我个人的偏见。

## WordPress 的 Gatsby 源码插件

盖茨比有很多官方插件。它们的结构是相似的，但是 Gatsby 确实提供了一些标准术语，以便于识别它的用途。[https://www.gatsbyjs.org/docs/naming-a-plugin/](https://www.gatsbyjs.org/docs/naming-a-plugin/)。

最初，我决定使用 Contentful 作为我的后端，这个插件是`gatsby-source-contentful`(看看按照标准惯例命名它有什么帮助)。Contentful 插件提供了所有的文章作为 GraphQL 中的 Markdown 节点，因此，所有“Remark”的“转换”插件都可以在这些文章上使用。现在，用于“转换”降价数据的“备注”转换插件棒极了。使用它们处理内容丰富的数据是一种享受。

为了从 WordPress 获取数据到 Gatsby，我们使用了一个“源”插件`gatsby-source-wordpress`。我将在另一篇文章中讨论我使用 WordPress 的原因。但是我面对的这个插件的主要问题是它从 WordPress REST API 查询数据，然后创建 GraphQL 模式用于 Gatsby。但是 WordPress REST API 默认只返回 HTML 格式的内容。所以即使你用一些 WordPress 插件(我用的是[WP Githuber MD])把你的帖子写成 Markdown，REST API 也会返回最终的内容。然而，这对 WordPress 是有意义的，因为它们的主题输出总是 HTML。但是我需要 Markdown，因为我想使用那些 transformer 插件，它们只在 Markdown 节点上工作。有很多 Github 的问题，比如这里的[https://github.com/gatsbyjs/gatsby/issues/6799 的](https://github.com/gatsbyjs/gatsby/issues/6799)。即使 WordPress Markdown 插件公开了单独的 REST 端点，Gatsby 源代码插件也需要支持这些。我没想找这样的插件，也没想黑盖茨比的官方源码插件。😀

## Turndown -将 HTML 转换为 Markdown

所以我想寻找一个可以将 HTML 转换成 Markdown 的解决方案。由于我一直是一个 DIY 的家伙，我开始在 ASTs 上阅读，并开始自己编写从 HTML 到 Markdown 的转换。我花了三天时间，有了一个工作版本。但是有很多错误。我意识到我太傻了。肯定已经有包裹了。输入[拒绝]。太棒了。转换几乎是完美的。所以我抛弃了我的转换库，转而写了一个本地的 Gatsby 插件，它采用 WordPress Post(或 Page)节点，并使用 Turndown 创建了一个 Markdown 节点。

## 插件`gatsby-transformer-wordpress-markdown`

我按照 Gatsby 命名标准给插件命名。“gatsby-transformer-WordPress-markdown”文件夹位于根 Gatsby 项目的 plugins 文件夹下。

该文件夹有 3 个文件:

```
├── gatsby-node.js
├── index.js
└── package.json 
```

`index.js`只包含一行`// noop `。

`package.json`包含插件和`turndown`包的名称，作为对`yarn add turndown`和`yarn add turndown-plugin-gfm`的依赖。

主要的主力是`gatsby-node.js`。
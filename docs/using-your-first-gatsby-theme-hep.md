# 使用你的第一个盖茨比主题

> 原文：<https://dev.to/hagnerd/using-your-first-gatsby-theme-hep>

盖茨比刚刚宣布了主题的稳定发布，并随之发布了一系列与主题相关的内容。这篇文章将介绍什么是主题，为什么它们可能对你有用，以及如何使用你的第一个主题。在后面的帖子中，我将深入探讨组件阴影等主题，甚至可能创作自己的主题。

如果你想一头扎进深水区，杰森·伦斯托夫发布了一个免费的关于盖茨比主题的知识分子课程。

## 什么是主题？

主题这个名字可能会让人认为它们只与视觉内容相关，然而盖茨比主题远不止于此。它们可以包括一些默认配置，设置转换器，插件，甚至其他子主题。主题可以*也可以*包括视觉风格和组件。

主题是可配置的，可重写的，最好的是，可组合的。

## 为什么主题有用？

如果你过去用过盖茨比，你可能会用一个启动器来启动你的网站。在主题之前，开头是一个很好的垫脚石。他们允许你启动一个 Gatsby 站点，这个站点配置了一些开箱即用的附加功能，比如支持 Mdx，或者使用一个外部服务，比如 Shopify。

然而，如果你想快速启动一个同时支持 Mdx 和 Shopify 的网站，你必须找到一个配置了两者的启动器，使用其中一个启动器，并找出如何设置其他功能，或者从头开始配置一切。

主题改变了这一点。我们可以很容易地制作一个`this`和`that`，而不是从一个`this`或`that`开始。记住，主题是可配置的、可重写的和可组合的。

## 我们开始吧

我们从简单的开始。让我们从零开始建立一个盖茨比网站，然后用`gatsby-theme-blog`快速自举一个博客的基本功能。

如果你想看完整的代码，请查看 Github repo 。

## 设置目录

首先我们需要为我们的 Gatsby 网站制作一个目录，然后我们需要初始化它。

```
mkdir first-gatsby-theme
cd first-gatsby-theme
yarn init -y 
```

> 我用的是`yarn`，但是如果你愿意，也可以随意使用`npm`。

## 安装我们的依赖项

我们可以通过使用`gatsby new <STARTER>`命令来使用启动器，但是让我们手动设置一个。这非常容易做到。

我们只需要`react`、`react-dom`、`gatsby`就可以开始了。所以让我们安装这些。之后，我们打开根目录下的`package.json`文件，为了方便添加一些脚本。

```
yarn add react react-dom gatsby 
```

```
//  package.json  {  "name":  "first-gatsby-theme",  "version":  "1.0.0",  "license":  "MIT",  "dependencies":  {  "react":  "...",  "react-dom":  "...",  "gatsby":  "...",  },  "scripts":  {  "build":  "gatsby build",  "start":  "gatsby develop",  "clean":  "gatsby clean"  }  } 
```

> `...`只是占位符。您的`package.json`文件将包含您已经安装的特定版本。

## 检查盖茨比是否在工作

为了看到我们所有的辛勤工作得到了回报，让我们做一些内容，让盖茨比发挥它的魔力。

我们需要在`src/pages`制作一个目录。按照惯例，Gatsby 将在这里寻找内容转换成网站上的页面，并为我们处理路由。

```
mkdir -p src/pages
touch src/pages/index.js 
```

> mkdir -p 在提供的路径中创建所有缺失的目录

`src/pages/index.js`将被映射到我们网站的根路径“/”。

```
// src/pages/index.js

import React from 'react'

export default function HomePage() {
  return (
    <>
      <h1>Welcome</h1>
      <p>Hello, from Gatsby <span role="img" aria-label="hand emoji waving hello">👋</span></p>
    </>
  )
} 
```

现在启动盖茨比网站，你需要做的就是运行。

```
yarn start 
```

您应该会看到类似这样的内容。
[![](img/7906e1503204bae03d6f51846b2584a4.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--YsY0WOr6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jn2c89iq0772vi4g01a9.png)

我知道，很刺激。

## 安装主题

现在我们知道盖茨比已经开始工作了，让我们安装一个主题。每个人都说他们想有一个博客，但开始很难。好消息是主题让你很容易开始，你不会有任何借口。

```
yarn add gatsby-theme-blog 
```

在项目的根目录下创建一个`gatsby-config.js`文件。

```
touch gatsby-config.js 
```

并添加以下

```
module.exports = {
  plugins: [
    'gatsby-theme-blog'
  ]
} 
```

在主题稳定发布之前，你的主题曾经生活在一个叫做`__experimentalThemes`的附加属性下，但是现在它们稳定了，它们就像插件一样！

如果你试图在此刻启动你的盖茨比网站，你会得到一些错误。让我们在接下来的两节中找出原因。

## 探索主题选项

现在还没有 gatsby-theme-blog 的 Github 回购，但是如果你看一下 [npm 页面](https://www.npmjs.com/package/gatsby-theme-blog)，你可以看到一个叫做`Usage`的部分。

[![](img/81b253f9688e3498180cedf3800ea4a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BgX5OGJV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4j4zppm41wkuecivw89s.png)

大多数 Gatsby 插件和主题都有你可以设置的选项。对于`gatsby-theme-blog`,有四个选项，每个选项都有默认值，这意味着我们可以不传递任何选项，主题仍然可以工作。

话虽如此，它还是有一些期待的。现在`gatsby-theme-blog`希望在`content/posts`目录中有一些 Markdown 或 MDX 形式的博客文章，在`content/assets`目录中有一个标题为`avatar`的图片。

如果没有这两者中的任何一个，你得到的错误都是一种隐晦的错误，有点令人失望。

现在，我们将把选项设置为默认值。如果你想覆盖任何你会改变你的`gatsby-config.js`。

```
// gatsby-config.js
module.exports = {
  plugins: [
    {
      resolve: 'gatsby-theme-blog',
      options: {
        contentPath: 'content/posts', // the file path to your blog posts
        basePath: '/', // the url for the root of your blog
        assetPath: 'content/assets', // the file path to your assets folder
        mdx: true, // whether or not to configure mdx for you
      }
    }
  ]
} 
```

这些是默认值，但是要更改其中任何一个值，请在 options 对象中设置您想要的值。

## 添加内容

现在我们知道了为什么我们的 Gatsby 网站在添加这个主题后会失败，让我们使用默认选项作为我们需要做什么来让我们的网站再次工作的指南。

首先，我们需要制作必要的文件夹。在项目的根目录下，我们将创建`content`、`content/posts`和`content/assets`目录。

```
mkdir -p content/{posts,assets} 
```

> 这里的{}叫做大括号展开，相当于运行`mkdir -p content/posts`和`mkdir -p content/assets`，只是方式更短。

现在我们已经有了文件夹，我们需要写一篇博文，并添加一个头像。

在您的`content/posts`目录中创建一个名为`hello-world.md`的文件，并添加您想要的任何内容。

```
---
title: "Hello,  World"
---

The worlds greatest blog post! 
```

接下来，在`content/assets`中保存一张自己名为`avatar`的照片。

我们需要做的第三件事是暂时删除我们的`src/pages/index.js`页面，因为`gatsby-theme-blog`默认将博客的根目录设为“/”根路径。

```
mv src/pages/index.js src/pages/_index.js 
```

如果你现在运行`yarn start`，一切都应该正常，你会看到这样的东西:
[![](img/8717b1aad196d66b1b9ee208acdba04d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t_AACB-8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9i03lm2b5qktigct8t94.png)

## 站点/作者信息

`gatsby-theme-blog`做的另一件事是在我们的`gatsby-config.js`中寻找一些站点元数据。

最后一次打开您的`gatsby-config.js`,添加您的信息。

```
module.exports = {
  siteMetadata: {
    title: "Awesome Blog", // Enter the title of your blog here
    author: "Matt Hagner", // Change this to your name
    description: "A really cool blog",
    social: [
      {
        name: 'twitter',
        url: 'twitter.com/_hagnerd',
      },
      {
        name: 'github',
        url: 'github.com/hagnerd',
      },
    ]
  },
  plugins: [
    'gatsby-theme-blog',
  ]
} 
```

* * *

希望这不会太痛苦，并有助于强调安装和设置一个盖茨比主题有多快。最好的部分是，你可以根据需要安装更多的主题，而不需要你从根本上改变你的网站结构。

* * *

## 接下来

我们博客的快速设置很棒，但是如果我们想改变风格呢？或者一些默认组件是如何渲染的？为此，我们将使用一个称为阴影的功能。

当它完成的时候，我会更新一个帖子的链接。
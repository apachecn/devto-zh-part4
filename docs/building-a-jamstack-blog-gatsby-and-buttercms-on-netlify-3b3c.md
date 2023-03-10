# 建立一个 JAMstack 博客:Netlify 上的 Gatsby 和 ButterCMS

> 原文：<https://dev.to/jakelumetta/building-a-jamstack-blog-gatsby-and-buttercms-on-netlify-3b3c>

这篇文章最初发布在 ButterCMS 的博客上

*   [jam stack 简介](#introductiontojamstack)
*   [jam stack 的优势](#advantagesofjamstack)
*   [jam stack 的缺点](#disadvantagesofjamstack)
*   [使用 Gatsby 和 JAMstack 的简单工作流程](#simpleworkflowusinggatsbyandjamstack)
*   [静态降价数据的挑战](#challengeswithstaticmarkdowndata)
*   [使用 ButterCMS 解决静态降价数据](#usingbuttercmstosolvestaticmarkdowndata)
*   [移除降价支持](#removingmarkdownsupport)
*   [将 ButterCMS 与 Gatsby 整合](#integratingbuttercmswithgatsby)
*   [从 ButterCMS 获取数据](#fetchingdatafromButterCMS)
*   [部署到网络生活](#deployingtonetlify)

在本教程中，我们将创建一个**快如闪电**，由 [Netlify](https://www.netlify.com/) 托管的静态博客应用程序，在前端使用 [Gatsby](https://www.gatsbyjs.org/) 和 [ButterCMS](https://buttercms.com/) 来提供内容。因此，我们留给你一个低成本的博客，你的内容营销团队可以在没有你的帮助下更新。

## **![jamstack blog](img/225c372627d9b2a99f68027e292a5772.png)T4】**

## **jam stack 简介**

JAMstack 是一种创建快速安全的网站和动态应用程序的方法，它使用 JavaScript、API 和标记服务，无需 web 服务器。

所有请求都由运行在客户端的 **JavaScript** 处理。我们可以使用任何 JavaScript 框架或库作为 JAMstack 的一部分。

所有的服务器端操作都被抽象成可重用的**API**。这些可以通过 HTTPS 访问，可以由第三方或定制方处理。

整个网站只是一个静态的 HTML，通常是在构建期间从像 **Markdown** 这样的文件中生成的。它也可以在静态站点生成器的帮助下生成。

## **jam stack 的优势**

JAMstack 的优势是多方面的。默认情况下，它们是**快速**和**安全**。它们通过 **CDN** 托管，通过 **HTTPS** 访问。它们不贵，因为有许多主机提供商为静态网站提供免费主机服务。

除了这些优势，JAMstack 还可以让 **atomic 部署自动构建**。他们也有更好的**开发者体验**。此外，一切都在版本控制中，这使得跟踪问题和回滚版本更容易处理。

我们也不一定需要编写任何代码来保存数据库中的数据。因此，开发运营开销大大减少。

## **jam stack 的缺点**

与 JAMstack 的优点相比，它的缺点要少得多。

虽然我们可以用 JAMstack 非常容易地创建一个站点，但是我们确实需要掌握 JavaScript 知识。如果我们不太了解 JavaScript，使用 JAMstack 可能不是一件容易的事。

如果我们没有为我们的 API 使用第三方 CMS 提供商，更新我们网站上的内容仍然需要开发者。对于没有 CMS 的非开发人员来说，这不是一个好的体验。

## **使用 Gatsby 和 JAMstack 的简单工作流程**

在这一节中，我们将创建一个静态博客应用程序，它将在前端使用 Gatsby，后端将由 [ButterCMS API](https://buttercms.com/docs/api/) 支持。

如果您没有 ButterCMS 帐户，请前往 [ButterCMS](https://buttercms.com/) 创建一个免费试用帐户。

我们需要在全球[安装](https://www.gatsbyjs.org/docs/gatsby-cli/#how-to-use) [Gatsby CLI](https://www.gatsbyjs.org/docs/gatsby-cli/) 来创建一个新的 Gatsby 站点。我们可以使用以下命令全局安装 Gatsby CLI:

```
npm install -g gatsby-cli 
```

一旦完成，我们可以使用下面的命令创建一个新的 Gatsby 站点:

```
gatsby new gatsby-buttercms-app https://github.com/ghoshnirmalya/gatsby-tailwind-minimal-starter 
```

这将克隆 https://github . com/ghoshnirmalya/Gatsby-tailwind-minimal-starter[Gatsby starter 样板文件](https://www.gatsbyjs.org/docs/starters)，并为我们生成一个新的应用程序:

[![jamstack blog](img/e31cde80defc66fecfbf213856f31c39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lbpTRy4J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.buttercms.com/SHlfIVY9Qz2Z6BD0zewm)

它还将在新应用程序中创建第一个提交。让我们进入目录并启动我们的应用:

```
cd gatsby-buttercms-app && yarn dev 
```

它将生成 Gatsby 所需的一系列配置。最后，我们应该能够看到如下所示的屏幕:

[![undefined](img/57bac6b67a3f14dcb80e1bd0e74bcc27.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q1HQXRcK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.buttercms.com/EKmGPjKLRrSYXSz85rf7)

我们现在可以在 [http://localhost:8000/](http://localhost:8000/) 访问我们的应用程序，并且可以在[http://localhost:8000/_ _ _ GraphQL](http://localhost:8000/___graphql)访问 graph QL 端点。如果我们在 [http://localhost:8000/](http://localhost:8000/___graphql) 访问我们的应用程序，我们现在可以看到以下屏幕:

[![jamstack blog](img/3c9bed7144842684e369473a38afb429.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hkF_z4Ku--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.buttercms.com/RN1hmP4TFWsdJh5UH9Q6)

您可以查看[提交](https://github.com/ghoshnirmalya/gatsby-buttercms-app/commit/f398a9b9871b7d28d5dbef5080fe2f80c13184db)中的更改。

我们的应用程序目前只包含静态数据。让我们为我们的应用程序添加 Markdown 支持，这样我们就可以在 Markdown 中写博客，这些博客将显示在我们的应用程序上。首先，我们需要停止正在运行的服务器，然后将下面的 Gatsby 插件添加到我们的应用程序中:

```
yarn add gatsby-source-filesystem gatsby-transformer-remark 
```

上面的命令将把[盖茨比源文件系统](https://www.gatsbyjs.org/packages/gatsby-source-filesystem/)和[盖茨比变压器备注](https://www.gatsbyjs.org/packages/gatsby-transformer-remark/)包安装到我们的应用程序中。 **gatsby-source-filesystem** 是一个源代码插件，用于从我们的本地文件系统向我们的 gatsby 应用程序提供数据，并且**Gatsby-transformer-Remark**使用 [Remark](https://remark.js.org/) 解析 Markdown 文件。

我们还必须更新我们的`**gatsby-config.js**`文件，将这些包包含在我们的应用程序的 Gatsby 插件系统中:

```
{
  resolve: `gatsby-source-filesystem`,
  options: {
    name: `markdown-pages`,
    path: `${__dirname}/content`
  }
},
'gatsby-transformer-remark' 
```

在这里，我们让 Gatsby 知道我们所有的减价内容都将出现在**内容**目录中。因此，让我们在应用程序的根目录下创建一个**内容**目录，并添加一个示例减价文件`**post-1.md**`。它将包含以下内容:

```
---
path: '/blogs/post-1'
date: '2019-08-04'
title: 'My first post'
---

Lorem ipsum dolor sit amet. 
```

上述博文的永久链接将是 **/blogs/post-1** 。

我们需要在根目录下创建一个名为 **gatsby-node.js** 的新文件，内容如下:

```
const path = require('path')

exports.createPages = ({ actions, graphql }) => {
  const { createPage } = actions
  const template = path.resolve('src/templates/blog-post.tsx')

  return graphql(`
    {
      allMarkdownRemark(
        sort: { order: DESC, fields: [frontmatter___date] }
        limit: 1000
      ) {
        edges {
          node {
            frontmatter {
              path
            }
          }
        }
      }
    }
  `).then(result => {
    if (result.errors) {
      return Promise.reject(result.errors)
    }

    return result.data.allMarkdownRemark.edges.forEach(({ node }) => {
      createPage({
        path: node.frontmatter.path,
        component: template,
        context: {
          slug: node.slug,
        },
      })
    })
  })
} 
```

这个文件负责为我们的每篇博客文章创建单独的页面。每个帖子的永久链接是在 Markdown 文件本身中定义的。

我们只需要为博客文章模板再创建一个文件。我们可以在 **src/templates** 中创建一个名为 **blog-post.tsx** 的新文件，内容如下:

```
import * as React from 'react'
import { graphql } from 'gatsby'

import Layout from '../components/layout'

interface BlogPostProps {
  data: {
    markdownRemark: {
      html: string
      frontmatter: {
        title: string
        date: string
      }
    }
  }
}

export default function Template({ data }: BlogPostProps) {
  const { markdownRemark } = data
  const { frontmatter, html } = markdownRemark

  return (
    <Layout>
      <div className="max-w-2xl mx-auto">
        <h1 className="font-bold mb-8 text-4xl">{frontmatter.title}</h1>
        <h2 className="font-semibold mb-8 text-base">Published on {frontmatter.date}</h2>
        <div className="text-xl font-serif leading-relaxed" dangerouslySetInnerHTML={{ __html: html }} />
      </div>
    </Layout>
  )
}

export const pageQuery = graphql`
  query($path: String!) {
    markdownRemark(frontmatter: { path: { eq: $path } }) {
      html
      frontmatter {
        date(formatString: "MMMM DD, YYYY")
        path
        title
      }
    }
  }
` 
```

这个文件负责创建单个博客文章页面的布局。

如果我们重启我们的服务器，我们可以在[http://localhost:8000/blogs/post-1](http://localhost:8000/blogs/post-1)看到新的博文:

[![undefined](img/7e82ad2b85152e40023b08a9a13ccbc6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8kHdnpbQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.buttercms.com/f8xU72yiSbqVKzEGYPNc)

您可以查看[提交](https://github.com/ghoshnirmalya/gatsby-buttercms-app/commit/72031a402f3c4329041ca944263c6ca06431204a)中的更改。

目前，在我们的应用程序中，我们支持 Markdown。每当我们在我们的**内容**目录中添加一个新的 Markdown 文件，Gatsby 就会为我们创建一个新的 url。我们可以在我们的**内容**目录中添加一个名为`**post-2.md**`的新文件，内容如下:

```
---
path: '/blogs/post-2'
date: '2019-08-04'
title: 'My second post'
---

Lorem ipsum dolor sit amet. 
```

如果我们现在访问[http://localhost:8000/blogs/post-2](http://localhost:8000/blogs/post-2)，我们可以查看新的博文。

[![undefined](img/50917ead682d0ff1de1d1038d2162ab5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vlC_Jeg9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.buttercms.com/Mg3wE3p9SReIdCfNp1rZ)

您可以查看[提交](https://github.com/ghoshnirmalya/gatsby-buttercms-app/commit/cdab415920593e2a5758596b8b195b2bd39027a1)以了解变更。

## **挑战静态降价数据**

用 Markdown 写博客很容易，只需要很少的时间就可以将这些变化部署到我们的实时站点上。然而，不擅长 Markdown 的人需要使用工具将他或她的文档转换为 Markdown。

除此之外，任何变更都需要在版本控制中，以便自动化构建能够工作。这些自动化构建是由版本控制的变化触发的。开发人员通常非常擅长维护版本。然而，非开发人员将需要使用一些工具来处理这个问题。

确保将最新的 JAMstack 文章直接发送到您的收件箱。

## **使用 ButterCMS 解决静态降价数据挑战**

如果我们的 JAMstack 站点由一些像 [ButterCMS](https://buttercms.com/) 这样的无头 CMS 提供支持，那么 Markdown 的问题就可以解决。

我们可以在 WYSIWYG 编辑器中编写我们的数据，并在我们的网站上发布这些变化。我们不必等待自动化部署，因为来自我们站点的数据是使用[ButterCMS API](https://buttercms.com/docs/api/)获取的。因此，在我们的网站上发布更新变得非常容易，而且更省时。

## **移除降价支持**

在本节中，我们将删除我们的应用程序中的降价支持，因为当您使用 ButterCMS 时不需要它。

让我们从移除我们不需要的包开始。我们可以从我们的`**package.json**`文件中删除**盖茨比源文件系统**和**盖茨比变压器备注**。我们还需要从我们的`**gatsby-config.js**`文件中删除插件。我们需要从`**gatsby-config.js**`文件中删除以下几行:

```
{
  resolve: 'gatsby-source-filesystem',
  options: {
    name: 'content',
    path: `${__dirname}/content`,
  },
},
'gatsby-transformer-remark', 
```

我们也可以删除我们的`**gatsby-node.js**`和`**src/templates/blog-post.tsx**`文件，因为不再需要了。现在，只有 [http://localhost:8000/](http://localhost:8000/) 网址可用。页面[http://localhost:8000/blogs/post-1](http://localhost:8000/blogs/post-1)和[http://localhost:8000/blogs/post-2](http://localhost:8000/blogs/post-2)将不再可用，因为我们已经删除了它们。

## **将巴特姆斯与盖茨比结合**

在这一节中，我们将把 ButterCMS API 与我们的 Gatsby 应用程序集成在一起。

我们需要在我们的应用程序
中安装 **gatsby-source-buttercms** 包

```
yarn add gatsby-source-buttercms 
```

我们还必须更新我们的`**gatsby-config.js**`文件，将这个包包含在我们应用程序的 Gatsby 插件系统中:

```
{
  resolve: 'gatsby-source-buttercms',
  options: {
    authToken: process.env.GATSBY_BUTTER_CMS_API_KEY,
  },
}, 
```

我们需要在根目录下创建一个新的`**.env.development**`文件，内容如下:

```
GATSBY_BUTTER_CMS_API_KEY=your_buttercms_api_key 
```

您可以从您的[仪表板](https://buttercms.com/home/)中获取 ButterCMS API 密钥。

我们还需要将以下内容添加到我们的`**gatsby-config.js**`文件中，以添加对环境变量的支持:

```
require('dotenv').config({
  path: `.env.${process.env.NODE_ENV}`
}) 
```

在您的代码库中不泄露凭证总是一个好主意。因此，我们使用环境变量。

让我们再添加一个新的`**gatsby-node.js**`文件，内容如下:

```
const path = require('path')

exports.createPages = ({ actions, graphql }) => {
  const { createPage } = actions

  const template = path.resolve(`src/templates/blog-post.tsx`)

  return graphql(`
    {
      allButterPost {
        edges {
          node {
            slug
          }
        }
      }
    }
  `).then(result => {
    if (result.errors) {
      return Promise.reject(result.errors)
    }

    result.data.allButterPost.edges.forEach(({ node }) => {
      createPage({
        path: `/blogs/${node.slug}`,
        component: template,
        context: {
          slug: node.slug,
        },
      })
    })
  })
} 
```

我们还需要一个新的博客帖子模板。让我们在`**src/templates/blog-post.tsx**`创建一个新文件`**blog-post.tsx**`，内容如下:

```
import React from 'react'
import { graphql } from 'gatsby'

import Layout from '../components/layout'

interface BlogPostProps {
  data: {
    butterPost: {
      date: string
      body: string
      title: string
    }
  }
}

export default function Template({ data }: BlogPostProps) {
  const { title, date, body } = data.butterPost

  return (
    <Layout>
      <div className="max-w-2xl mx-auto">
        <h1 className="font-bold mb-8 text-4xl">{title}</h1>
        <h2 className="font-semibold mb-8 text-base">Published on {date}</h2>
        <div className="text-xl font-serif leading-relaxed" dangerouslySetInnerHTML={{ __html: body }} />
      </div>
    </Layout>
  )
}

export const pageQuery = graphql`
  query($slug: String!) {
    butterPost(slug: { eq: $slug }) {
      title
      body
      date
      meta_description
      status
      tags {
        name
      }
    }
  }
` 
```

您可以查看[提交](https://github.com/ghoshnirmalya/gatsby-buttercms-app/commit/b3e24ec6df31d384c96d98c5915f10f41c36928b)以了解变更。

## **从 ButterCMS 获取数据**

在本节中，我们将在 ButterCMS 中创建新的博客文章。

我们可以通过我们的[仪表盘](https://buttercms.com/post/)创建一篇博文。一旦我们写了一篇文章，我们可以通过点击**发布**按钮来发布:

[![undefined](img/5ca3d58c2665fabed18fb3988ba4bfda.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ulewdiJx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.buttercms.com/Ksj5w5XLQNSC4YqIcn2k)

如果我们没有手动修改我们页面的 **URL slug** ，ButterCMS 将为我们生成一个，我们可以从 **SEO** 标签中查看该 slug:

[![undefined](img/425c73144a066c46328e46a4fc589796.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gu-7ERRg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.buttercms.com/MK3leKADT2uuPS1f2H8X)

现在，如果我们重启应用程序的服务器，我们可以在[http://localhost:8000/blogs/aenean-id-velit-felis](http://localhost:8000/blogs/aenean-id-velit-felis)查看新的博客文章:

## **正在向网络部署**

在本节中，我们将把我们的应用程序部署到 [Netlify](https://www.netlify.com/) 。

一旦我们在 [Netlify](https://www.netlify.com/) 有了账户，我们就可以在这里从[创建一个新网站。](https://app.netlify.com/start)

[![undefined](img/cbbf53ec2a227419f8fcbb35f63c181e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HmZXxgaB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.buttercms.com/Dr1Cr3DBT8CaLZ51NjTc)

我们可以搜索您要部署的存储库:

[![undefined](img/1a597efc821e9bd8229afb32b7ac95e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ij6DR8R4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.buttercms.com/8dqj6ARgTyGilysk613i)

下一步，我们需要在**高级构建设置**中添加**GATSBY _ BUTTER _ CMS _ API _ KEY**环境变量:

[![undefined](img/f6a6ab978301c8aa4cc839f622e04199.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cb9fBQgw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.buttercms.com/vrO7CZZSAW5PHX5lEItQ)

一旦网站部署完毕，我们就可以在[https://gatsby-buttercms-app.netlify.com/](https://gatsby-buttercms-app.netlify.com/)现场观看。在您的情况下，url 会有所不同。我们可以从项目概述部分查看我们网站的 url:

[![undefined](img/d6c767c5fa678e51debd10205c204f6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yXbAyDaK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.buttercms.com/1MbQRapSMeACRPQS9CDN)

我们还可以在现场观看其他博客文章。

现在，每次我们推送到 Github 存储库的主分支时，更改将会自动部署。

让我们将所有的博客文章都显示在我们的登录页面上，这样我们就可以从一个地方查看所有的文章。

我们只需要把`**src/pages/index.tsx**`的内容替换成下面的:

```
import * as React from 'react'
import { graphql, Link } from 'gatsby'

import Layout from '../components/layout'

interface BlogsProps {
  data: {
    allButterPost: {
      edges: [
        {
          node: {
            id: string
            slug: string
            title: string
            meta_description: string
          }
        }
      ]
    }
  }
}

const BlogsPage = ({ data }: BlogsProps) => {
  const posts = data.allButterPost.edges

  return (
    <Layout>
      <div className="flex flex-wrap -mx-4">
        {posts.map(({ node }) => {
          return (
            <div key={node.id} className="w-full sm:w-1/2 md:w-1/3 lg:w-1/4 mb-8 px-4">
              <Link key={node.id} to={`/blogs/${node.slug}`}>
                <div className="max-w-sm rounded overflow-hidden shadow-lg bg-white">
                  <div className="p-8">
                    <div className="font-bold text-xl mb-4">{node.title}</div>
                    <p className="text-gray-700 text-sm">{node.meta_description}</p>
                  </div>
                </div>
              </Link>
            </div>
          )
        })}
      </div>
    </Layout>
  )
}

export default BlogsPage

export const pageQuery = graphql`
  query {
    allButterPost {
      edges {
        node {
          id
          slug
          title
          status
          meta_description
        }
      }
    }
  }
` 
```

这将在我们的登录页面上显示我们的所有博客文章:

[![image5.png](img/225c372627d9b2a99f68027e292a5772.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pHJRGVtC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.buttercms.com/zFJwLE2Rp68qrzhPzEsU)

您可以查看[提交](https://github.com/ghoshnirmalya/gatsby-buttercms-app/commit/58bc75fa22179c4add18e9d87e42e11de1460343)以了解变更。

一旦我们将更改推送到 Github，我们就可以在 https://gatsby-buttercms-app.netlify.com/观看直播了。

## **结论**

正如你现在看到的，我们已经做了一个漂亮的 JAMstack 站点，由 ButterCMS APIs 提供支持。

希望这篇教程对你以后的项目有所帮助。请在下面的评论区分享您的反馈。

要了解更多 JAMstack 更新，请订阅我们的每月简讯。
# 包含 Next.js、Contentful & Netlify 的静态站点

> 原文：<https://dev.to/andreasbergqvist/static-site-with-next-js-contentful-netlify-1h7e>

### TL；速度三角形定位法(dead reckoning)

代码:[https://github.com/itiden/itiden.se/tree/blog](https://github.com/itiden/itiden.se/tree/blog)(博客分支)
结果: [https://itiden.se](https://itiden.se) (对，是瑞典语，抱歉...)

编辑 2020-02-17:
网页已更新。它不再像以前那样令人满意了。查看博客 bransch，了解这篇博文中引用的代码。

## 免责声明

这是一个复制我们开源网站的指南。您将能够设置一些虚拟内容的内容。但是要确保你把它看作是你网站的模板。要能够编辑关于 Next.js / React 的站点知识是必需的。

你可以随意问任何问题，但这个想法是为了让你用一个在 React 中开发的站点来测试托管在 Netlify 上的 Contentful 变得容易。

## 堆栈

因此，在 itiden，我们想要一个网站，在那里我们可以将 Contentful 作为“云 CMS ”,并在 Netlify 上托管它。

因为我们以前有使用 Next.js (React)的经验，所以我们决定在构建页面时使用它。

它是用 Typescript 编写的，我们使用 Tailwind 作为“CSS 框架”,样式化的组件作为“JS 中的 CSS”库。

该代码在 Github([https://github.com/itiden/itiden.se](https://github.com/itiden/itiden.se))上是开源的，你可以随意克隆它，并将其用作你自己网站的模板

## 设置项目

点击[https://github.com/itiden/itiden.se](https://github.com/itiden/itiden.se)上的“使用此模板”按钮，创建您自己的回购副本

有了 repo 后，克隆它并将. env.example 复制到。包络和运行纱线。

```
cp .env.example .env
yarn 
```

现在您需要设置 Contentful 来获取一些内容。如果您还没有帐户，请创建一个新帐户。之后，你需要创造一个空白的空间。

您的现在应该在一个屏幕上，要求您添加第一个内容类型。

[![](img/a0aacc33976bb22b441e253424fedd83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wtJ3Epqp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hvquuwq7qzgvad2p8h19.png)

当你在那个屏幕上时，通过运行`npm install -g contentful-cli`安装 Contentful CLI。
通过运行`contentful login`将其登录到您的帐户。
如果成功，您将收到一个代币，并保存在您的电脑上。

现在您需要导入一些内容类型和内容。回购中有一个`contentful-export.json`文件。

运行:
`contentful space import --space-id <your space id> --content-file contentful-export.json`

你可以在 contentful 的网址中找到你的空间 id:
[https://app.contentful.com/spaces/space_id/home](https://app.contentful.com/spaces/space_id/home)

现在重新加载内容丰富的页面，并单击“使用 Api”按钮。
在此页面上，将空间 id 和内容交付 API -访问令牌复制到您的。环境文件:

```
CONTENTFUL_SPACE=<space id>
CONTENTFUL_TOKEN=<access token> 
```

现在可以运行`yarn getcontent`从 Contenful 获取内容，然后运行`yarn dev`启动开发服务器。

转到 [http://localhost:3000/](http://localhost:3000/) ！

## 内容模式

在这里阅读内容模型:
[https://www . Content ful . com/r/knowledge base/Content-modeling-basics/](https://www.contentful.com/r/knowledgebase/content-modelling-basics/)

以下内容模型已在您的空间中创建。这些解释是关于我们如何使用它们的，但是你可以随心所欲地使用它们。

**菜单**
目前只有一个菜单项，那就是主顶层菜单。
主菜单包含**菜单项**。注意，名为“Case”的第一个菜单项没有菜单项。它在前端是硬编码的。

**菜单项**
一个菜单项有一个标签和一个对**页面**的引用

**页面**
一个页面有标题、Slug(用于生成 URL)、描述、页眉和正文。标题和正文是“富文本”字段，您可以在其中添加条目。目前，在前端呈现的唯一条目是**员工**内容模型。注意:第一页/被硬编码以显示**案例**内容。

**员工**
这些内容可以添加到**页面**的正文字段中。

**案例**
案例内容在首页列出。他们还会收到 url /case/。

**类别** / **技术** / **合作方**
这些都是添加到**案例**中的内容。

## 构建静态文件

应该将哪些页面导出到静态页面的配置存在于`next.config.js`文件中。

参见:
[https://github . com/itiden/itiden . se/blob/353817 c 013092470 b 4548603 fa 3114 aa 55 e 69093/next . config . js # L14](https://github.com/itiden/itiden.se/blob/353817c013092470b4548603fa3114aa55e69093/next.config.js#L14)

要创建静态文件，运行`yarn build`。
所有创建的文件将被导出到一个`out`文件夹中。

## 网络虚拟主机

如果需要，在 Netlify 上创建一个帐户。然后基于你的 github repo 开始一个新的站点。

当被询问时，将“构建命令”设置为`yarn build`，将“输出目录”设置为`out`。

转到设置/构建和部署/环境。

编辑环境并从您的。环境文件。
内容丰富空间=
内容丰富令牌=

转到部署并触发部署。

您现在已经收到了您网站的网址。类似于[https://strange _ text . netlify . com/](https://strange_text.netlify.com/)

下一步可能是设置[自定义域名](https://www.netlify.com/docs/custom-domains/)和 [HTTPS](https://www.netlify.com/docs/ssl/)

您现在有了一个静态创建的无服务器托管 React 站点！

## 从上下文触发重建

在 Contenful 的顶部菜单中，单击应用程序。
安装 Netlify 应用程序并连接您的帐户。
然后选择您创建的站点。

当编辑你的内容时，你现在有一个“建立”按钮和一个“打开预览”按钮来在 Netlify 上建立你的站点。

## 谷歌分析

要添加 Google Analytics，只需将您的跟踪 ID 添加到您的。env 和/或 Netlify 上的环境变量。

```
CONTENTFUL_SPACE=<space id>
CONTENTFUL_TOKEN=<access token>
GA=<tracking ID> 
```
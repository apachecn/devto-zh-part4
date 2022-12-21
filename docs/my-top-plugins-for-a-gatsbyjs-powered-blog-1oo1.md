# 我的盖茨比博客插件

> 原文：<https://dev.to/cole_ruche/my-top-plugins-for-a-gatsbyjs-powered-blog-1oo1>

有一段时间，我不太明白静态页面生成器的概念，尤其是 Gatsby JS。直到我决定试一试，我才发现这是多么神奇的技术！在所有其他超级事物中，Gatsby JS 被用来创建一个令人惊叹的快速博客，它可以使用各种数据源，如 Contentful CMS、WordPress 或 plain old Markdown。对于后者，它很容易设置和使用。

*这个牛逼的博客是盖茨比和 Markdown 一起建的。*

现在，虽然这不是一个用 Markdown 设置 Gatsby 的教程，但我将向你展示一些我目前为止遇到的很棒的插件，我在我的 Gatsby 博客网站上使用它们。其中一些插件有助于用户界面，UX，其他插件有助于集成有用的 but 服务，否则将很难(或者可能不会)设置。

> 其中一些插件是为 Markdown 设计的。但是他们中的大多数使用 Gatsby 站点，数据源来自任何地方。

不分先后，让我们开始吧:

### 盖茨比-插件-mailchimp

没有订阅者的博客算什么，对吧？这个插件使你的 [Mailchimp](https://mailchimp.com/) 账户和你的 Gatsby 博客链接起来变得非常简单，让你可以订阅邮件到你的 Mailchimp 列表。设置

就像运行`yarn add gatsby-plugin-mailchimp`安装包并添加下面的代码片段到你的`gatsby-config.js`文件:
一样简单

```
{
  resolve: "gatsby-plugin-mailchimp",
  options: {
    endpoint: // add your MC list endpoint here; see instructions below
  },
}, 
```

Enter fullscreen mode Exit fullscreen mode

欲了解更多信息，请访问页面[此处](https://www.gatsbyjs.org/packages/gatsby-plugin-mailchimp/)。

### 盖茨比-插件-程序

Gatsby JS 站点非常快，这就是静态站点生成器的概念。它们在不到一秒钟的时间内就能点击加载页面。但是如果你建过，或者访问过一个用盖茨比建的网站，你会同意我的看法，情况并不总是这样。

有时候，由于某些原因，他们在加载页面时会有一点延迟。大多数情况下，没有指示器向用户显示页面正在加载，他们可能会认为这是一个错误，最终可能会继续点击链接，或者完全离开。这就是`gatsby-plugin-nprogress`的用武之地。

根据 gatsby 插件库的说法，它*“当一个页面加载延迟时(Gatsby 认为是点击一个链接后的一秒钟)，会自动显示 nprogress 指示器。”*

设置`yarn add gatsby-plugin-nprogress`并将下面一行添加到`gatsby-config.js`文件:

```
// In your gatsby-config.js
plugins: [
  {
    resolve: `gatsby-plugin-nprogress`,
    options: {
      // Setting a color is optional.
      color: `tomato`,
      // Disable the loading spinner.
      showSpinner: false,
    },
  },
] 
```

Enter fullscreen mode Exit fullscreen mode

### 盖茨比-备注-prismjs

这个插件使用 [PrismJS](https://prismjs.com/) 为你的 markdown 文件中的代码块添加语法高亮。这使得您的代码片段、示例和代码块在美学上令人愉悦。

要用，`yarn add gatsby-transformer-remark gatsby-remark-prismjs prismjs`。这将安装所有需要的模块，包括官方的 PrismJS 库。关于定制选项和如何使用，请访问[他们的页面](https://dev.togatsby-transformer-remark%20gatsby-remark-prismjs%20prismjs)。

### 盖茨比-插件-disqus

这有助于您将您的 [Disqus](https://disqus.com/) 帐户链接到您的博客。它使您能够激活对您的博客页面的评论和反应。因此，它允许你和你的观众之间的互动。从这里，你可以得到反馈，看到你的读者对你的作品和写作的想法和感受。

关于用法和说明，请访问[这里](https://www.gatsbyjs.org/packages/gatsby-plugin-disqus/)。

### 盖茨比-插件-机器人-txt

这是一个自动为你的站点创建 robots.txt 的 gatsby 插件。根据[尼尔·帕特尔](https://neilpatel.com/blog/robots-txt/)，*“robots . txt 文件，也称为 robots exclusion protocol 或 standard，是一个文本文件，它告诉网络机器人(通常是搜索引擎)要抓取你网站上的哪些页面。”拥有一个`robots.txt`文件对 SEO 有好处，因为它告诉搜索引擎(例如谷歌机器人)如何抓取你的网站。要将它添加到您的站点，运行`yarn add gatsby-plugin-robots-txt`，对于最简单的实现，将下面的代码添加到您项目的`gatsby-config.js` :* 

```
plugins: ['gatsby-plugin-robots-txt'] 
```

Enter fullscreen mode Exit fullscreen mode

如需更多配置，请访问[插件页面](https://www.gatsbyjs.org/packages/gatsby-plugin-robots-txt/?=gatsby-plugin-robots-txt)

### 盖茨比-插件-清单

Gatsby 插件增加了一个`manifest.webmanifest`来使网站成为渐进式网络应用。这个插件附带的功能可以把你的应用变成一个[渐进式网络应用](https://www.google.com/url?sa=t&source=web&rct=j&url=https://developers.google.com/web/progressive-web-apps/&ved=2ahUKEwj_vruhuvTjAhUJShUIHSdHC_cQFjAhegQIBBAC&usg=AOvVaw0dIOwy-hAgSXFNdlBrXXwO)——自动图标生成、网站图标支持和缓存，所有这些都会被考虑进去。建议将此插件与 [gatsby-plugin-offline](https://www.gatsbyjs.org/packages/gatsby-plugin-offline/?=gatsby-plugin-offline) 配合使用，效果最佳。要使用，运行`yarn add gatsby-plugin-offline gatsby-plugin-manifest`。
打开你的配置文件，粘贴如下:

```
plugins: [
    {
      resolve: `gatsby-plugin-manifest`,
      options: {
        name: `GatsbyJS`,
        short_name: `GatsbyJS`,
        start_url: `/`,
        background_color: `#f7f0eb`,
        theme_color: `#a2466c`,
        display: `standalone`,
      },
    },
    'gatsby-plugin-offline'
  ], 
```

Enter fullscreen mode Exit fullscreen mode

确保用您自己的详细信息替换必要的行。

### 盖茨比-插件-谷歌-分析

这是用来添加[谷歌分析](https://www.google.com/url?sa=t&source=web&rct=j&url=https://analytics.google.com/analytics/web/&ved=2ahUKEwjoq6CRj_jjAhVPSxUIHaH9CB0QFjAAegQIBRAB&usg=AOvVaw095EntAfOjiijSk290zWyQ)到你的博客。这有助于你获得有用的信息，如人口统计，设备信息，网页等。它有助于了解哪些页面是最常访问的，哪个国家代表了大多数访问者，哪些设备是访问者最常使用的，从而帮助您针对特定的受众选择制作更好的内容。要使用，运行`yarn add gatsby-plugin-google-analytics`，然后将下面的代码添加到您的`plugins` :

```
{
  resolve: `gatsby-plugin-google-analytics`,
    options: {
       trackingId: `YOUR-TRACKING-ID`,
}, 
```

Enter fullscreen mode Exit fullscreen mode

如需更多选项和配置，请访问[插件页面](https://www.gatsbyjs.org/packages/gatsby-plugin-google-analytics/)

### 盖茨比-插件-网站地图

这个插件为你的站点生成一个站点地图。网站地图被高度推荐用于搜索引擎优化。为它添加一个插件会对你有很大好处。首先，通过运行`yarn add gatsby-plugin-sitemap`安装插件，然后将下面一行代码添加到您的 Gatsby 配置中:

```
plugins: [`gatsby-plugin-sitemap`] 
```

Enter fullscreen mode Exit fullscreen mode

上面一行是所需的最低配置，默认情况下，生成的站点地图将包含您站点的所有页面。如果出于某种原因，你不想要这种行为，需要排除一些页面，请访问[此页面](https://www.gatsbyjs.org/packages/gatsby-plugin-sitemap/?=)了解更多配置。

> 注意:这个插件只有在`production`模式下运行时才会产生输出！要测试你的站点地图，运行:`gatsby build && gatsby serve`

上面的插件只是 [Gatsby library](https://www.gatsbyjs.org/plugins/?=) 中超过 1000 个受支持的插件中的一小部分，但它们只是我工作过并喜欢的几个。一些插件捆绑在一些启动器中，你可能不需要自己安装。

我将非常高兴得到关于这些插件的反馈，并知道你喜欢哪些额外的插件。请在评论区告诉我们。
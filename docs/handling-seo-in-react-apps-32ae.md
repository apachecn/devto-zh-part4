# 在 React 应用中处理 SEO

> 原文：<https://dev.to/smakosh/handling-seo-in-react-apps-32ae>

> 最初发布于[我的博客](https://smakosh.com/seo-in-react-apps)

我已经成功地在巴厘岛着陆，租了一辆摩托车，享受着从我的住处到巴厘岛道场的旅程，我现在正在那里写这篇文章。

## SEO 定义

搜索引擎优化(SEO)是提高网站流量的质量和数量，增加网站或网页对网络搜索引擎用户的可见性的过程。

> 摘自维基百科

## SPA 问题与 SEO 神话

所有使用 CRA (create-react-app)或 Gatsby 构建来提供动态内容的 React 应用程序都是单页应用程序，这意味着只有一个 index.html 文件来呈现你的应用程序的内容，这意味着你的组件中的所有元标签都不会被搜索引擎抓取来进行索引，或者曾经是这样！截至 2019 年 5 月，[谷歌宣布](https://webmasters.googleblog.com/2019/05/the-new-evergreen-googlebot.html)其爬虫现在可以像 Twitter/脸书/Slack/Telegram 爬虫一样抓取 JavaScript，所以当任何非技术人员提到这个 SPA SEO 神话时，请确保启发他们。

## 爬虫之前我们做的事情，原来是可以爬 JavaScript 的

大多数 React 开发人员使用 Next Js，因为它提供服务器端呈现，这意味着当用户访问某个 url 或使用一种称为预呈现的技术时，页面会按需生成，这基本上是在构建时的服务器端呈现，Gatsby 仍然使用这种方法为静态内容生成页面，但不提供动态内容的功能，不像 Netlify 等一些服务为动态内容提供预呈现。

## SEO 组件

受这个 GitHub 库的启发，我制作了这个 SEO 组件，我几乎在我所有的 Gatsby/React 应用上重复使用它来处理 SEO

> 我在代码中添加了一些注释来解释每个部分

```
import React from 'react'
import Helmet from 'react-helmet'

// This is the thumbnail that appears when someone shares your website
import Thumbnail from 'assets/me.jpg'
import {
  url,
  defaultTitle,
  defaultDescription,
  social,
  socialLinks,
  address,
  contact,
  legalName,
  foundingDate,
  logo,
  author,
} from 'data/config'

export const SEO = ({
  title,
  type,
  description,
  articleBody,
  datePublished,
  dateModified,
  cover,
  location = '',
  readTime,
}) => {

// This is Structured data that is recommended to have according to Google
  // You can read more about it on Google's own documentation about structured data
  // The first string is for the Article schema and the second one for the organization schema
  const structuredDataArticle = `{
        "@context": "http://schema.org",
        "@type": "${type}",
        "mainEntityOfPage": {
            "@type": "WebPage",
            "@id": "https://google.com/article"
        },
        "headline": "${description}",
        "image": "${
      cover ? `https://smakosh.com${cover}` : `https://smakosh.com${Thumbnail}`
    }",
        "datePublished": "${datePublished}",
        "dateModified": "${dateModified}",
        "author": {
            "@type": "Person",
            "name": "${author}"
        },
        "articleBody": "${articleBody}",
        "publisher": {
            "@type": "Organization",
            "name": "${author}",
            "logo": {
                "@type": "ImageObject",
                "url": "${logo}"
            }
        },
        "description": "${description}",
        "url": "${url}${location}/?ref=smakosh.com"
  }`

  const structuredDataOrganization = `{
        "@context": "http://schema.org",
        "@type": "${type}",
        "legalName": "${legalName}",
        "url": "${url}",
        "logo": "${logo}",
        "foundingDate": "${foundingDate}",
        "founders": [{
            "@type": "Person",
            "name": "${legalName}"
        }],
        "contactPoint": [{
            "@type": "ContactPoint",
            "email": "${contact.email}",
            "telephone": "${contact.phone}",
            "contactType": "customer service"
        }],
        "address": {
            "@type": "PostalAddress",
            "addressLocality": "${address.city}",
            "addressRegion": "${address.region}",
            "addressCountry": "${address.country}",
            "postalCode": "${address.zipCode}"
        },
        "sameAs": [
            "${socialLinks.twitter}",
            "${socialLinks.google}",
            "${socialLinks.youtube}",
            "${socialLinks.linkedin}",
            "${socialLinks.instagram}",
            "${socialLinks.github}"
        ]
    }`

  return (
    // Notice I'm using react-helmet to inject these elements within the header tag
    <Helmet>
      {/* The description that appears under the title of your website appears on search engines results */}
      <meta name="description" content={description || defaultDescription} />

      {/* The thumbnail of your website */}
      <meta
        name="image"
        content={cover ? `${url}${cover}` : `${url}${Thumbnail}`}
      />

      {/* Opengraph meta tags for Facebook & LinkedIn */}
      <meta property="og:url" content={`${url}${location}/?ref=smakosh.com`} />
      <meta
        property="og:type"
        content={type === 'NewsArticle' ? 'NewsArticle' : 'website'}
      />
      <meta
        property="og:title"
        content={title ? `Smakosh | ${title}` : defaultTitle}
      />
      <meta
        property="og:description"
        content={description || defaultDescription}
      />
      <meta
        property="og:image"
        content={cover ? `${url}${cover}` : `${url}${Thumbnail}`}
      />

      {/* You can get this id when you create an app id on Facebook of your Facebook page */}
      <meta property="fb:app_id" content={social.facebook} />

      {/* These tags work for Twitter & Slack, notice I've included more custom tags like reading time etc... */}
      <meta name="twitter:card" content="summary" />
      <meta name="twitter:creator" content={socialLinks.twitter} />
      <meta name="twitter:site" content={social.twitter} />
      <meta
        name="twitter:title"
        content={title ? `Smakosh | ${title}` : defaultTitle}
      />
      <meta
        name="twitter:description"
        content={description || defaultDescription}
      />
      <meta
        name="twitter:image:src"
        content={cover ? `${url}${cover}` : `${url}${Thumbnail}`}
      />
      {type === 'NewsArticle' && (
        <meta name="twitter:label1" value="Reading time" />
      )}
      {type === 'NewsArticle' && (
        <meta name="twitter:data1" value={`${readTime} min read`} />
      )}
      {type === 'NewsArticle' && (
        <meta name="author" content="Ismail Ghallou" data-react-helmet="true" />
      )}
      {type === 'NewsArticle' && (
        <meta
          name="article:published_time"
          content={datePublished}
          data-react-helmet="true"
        />
      )}

      {/* Structured data */}
      <script type="application/ld+json">
        {type === 'NewsArticle'
          ? structuredDataArticle
          : structuredDataOrganization}
      </script>

      {/* Not sure if this is still relevant as Google shut down their Google+ paltform */}
      <link rel="publisher" href={socialLinks.google} />

      {/* The title of your current page */}
      {title ? `Smakosh | ${title}` : defaultTitle}

      {/* Default language and direction */}
      <html lang="en" dir="ltr" />
    </Helmet>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我把它放在每个组件上，它的作用就像一个页面，就像这样

```
import React from 'react'
import SEO from './SEO'

export default () => (
  <div>
    <SEO title="Home page" location="/" type="Organization" />
    <h1>Home page</h1>
  </div>
) 
```

Enter fullscreen mode Exit fullscreen mode

> 您可以从这个博客的代码源中获得这个组件。

## 提示&让你的网站在搜索结果中排名靠前的工具

如果你的网站是一个像这个网站一样的博客，强烈建议你有一个像[this](https://smakosh.com/rss.xml)这样的 RSS 订阅源，因为一些应用和扩展会抓取订阅源，会给你带来更多的访问者，从而提高你在搜索结果中的排名。

有一个网站地图也有帮助，你必须把它提交给你的谷歌搜索控制台。

> 你可以在这里测试你的结构化数据

欢迎在评论区留下你的问题，我会尽我所能回答所有的问题。
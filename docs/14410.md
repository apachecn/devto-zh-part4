# 动态网站地图与 Contentful 和 Next.js 又名'看马没有网络挂钩'

> 原文：<https://dev.to/mrispoli24/dynamic-sitemaps-with-contentful-and-next-js-a-k-a-look-ma-no-webhooks-3bj5>

在使用 headless 内容管理系统时，创建 sitemap.xml 文件一直困扰着我。“你说 Contentful 不做网站地图是什么意思？!"我的 SEO 同事会说——根本不理解 headless 的意思。这是像 wordpress 这样的老系统似乎已经稳操胜券的一件事。

## 我的早期方法

一年前，我制定了一个初步的解决方案，包括使用一个 chron 作业来定期创建文件。遗憾的是，大多数云托管提供商(Heroku & now.sh)不允许在构建完成后添加文件，所以你现在必须把它保存到像 S3 这样的 CDN 上。

后来，我尝试了一种方法，在 Contentful 内部的每个发布事件中，通过 webhook 触发站点地图的构建。这样做的问题是，你必须确保保存到 S3 内部的同一个 URL，并且你仍然有相同的添加 S3 依赖关系。

你可以在每个 webhook 事件上进行一次完整的重建来保存文件，这是许多静态站点的拥护者所乐于接受的。然而，随着你的网站变得越来越大(可能会处理很多钱)，让构建发生在下降的帽子只是让我感到不安。只是需要担心更多的活动部件。必须有一个更好的方法，我想用一个好的缓存来保持我的网站的动态性，并确保构建只发生在*代码*改变而不是*内容*改变的时候。我也想摆脱对 S3 的额外依赖。

## 新方法

幸运的是，Next.js 可以在它的`getInitialProps`钩子中完成这项工作，并轻松地提供 XML 文件。你可以设置站点地图页面，让它在服务器上构建，设置好就可以忘记了。

首先在 pages 目录中创建`sitemap.js`文件。

`touch ./pages/sitemap.js`

安装`xmlbuilder`包:

`npm install xmlbuilder`或`yarn add xmlbuilder`任你选择。

然后根据您的满意模型，按照您的喜好配置以下内容。我在这里使用一个`pages`和`articles`模型作为例子，但是你可能有更多。

```
import { createClient } from '../services/contentful';
import * as builder from 'xmlbuilder';

const rootUrl = 'https://yourhomepage.com';

const buildUrlObject = (path, updatedAt) => {
  return {
    'loc': { '#text': `${rootUrl}${path}` },
    'lastmod': { '#text': updatedAt.split('T')[0] },
    'changefreq': { '#text': 'daily' },
    'priority': { '#text': '1.0' }
  }
}

const Sitemap = () => ( null );

Sitemap.getInitialProps = async ({ res }) => {
  try {
    const client = createClient();

    const pages = await client.getEntries({ 
      content_type: 'page', 
      limit: 1000,
      include: 1 
    });

    const articles = await client.getEntries({ 
      content_type: 'article', 
      limit: 1000,
      include: 1 
    });

    let feedObject = {
      'urlset': {
        '@xmlns': 'http://www.sitemaps.org/schemas/sitemap/0.9',
        '@xmlns:image': 'http://www.google.com/schemas/sitemap-image/1.1',
        'url': []
      }
    }

    for (const item of pages.items) {
      if (typeof item.fields.slug !== 'undefined') {
        feedObject.urlset.url.push(
          buildUrlObject(`/${item.fields.slug === 'index' ? '' : item.fields.slug}`, item.sys.updatedAt)
        );
      }
    }

    for (const item of articles.items) {
      if (typeof item.fields.slug !== 'undefined') {
        feedObject.urlset.url.push(
          buildUrlObject(`/blog/${item.fields.slug}`, item.sys.updatedAt)
        );
      }
    }

    for (const item of posts.items) {
      if (typeof item.fields !== 'undefined') {
        feedObject.urlset.url.push(
          buildUrlObject(`/the-salon/${item.fields.slug === 'index' ? '' : item.fields.slug}`, item.sys.updatedAt)
        );
      }
    }

    const sitemap = builder.create(feedObject, { encoding: 'utf-8' });

    if (res) {
      res.setHeader('Cache-Control', 's-maxage=5, stale-while-revalidate');
      res.setHeader('Content-Type', 'application/xml');
      res.statusCode = 200;
      res.end(sitemap.end({ pretty: true }));
    }

    return;
  } catch(error) {
    return { error: 404 };
  }
};

export default Sitemap; 
```

*注意:我喜欢把我的内容服务提取到一个`services`目录中，但是你可以把内容包或者任何你想用的无头 CMS 放在这里。我还在 contentful 中的主页上使用了 slug `index`,所以我在这里使用了三进制检查来不包含 slug。再次根据需要进行配置。我也把文章和页面限制在 1000 篇以内，但是如果你有更多的文章和页面，你可能也想在这里做一些分页的魔术。*

## 部署

为了在 now.sh 上进行部署，您只需要打开您的`now.json`文件并进行相应的设置。还要确保在这里为您的`robots.txt`文件添加路线。这可以存储在静态，但你会希望它可以访问的路线。

```
 {
  "version": 2,
  "alias": "my-sitemap-sample",
  "name": "my-sitemap-sample",
  "builds": [{ "src": "next.config.js", "use": "@now/next" }],
  "routes": [
    { "src": "^/robots.txt",  "dest": "/static/robots.txt" },
    { "src": "/sitemap.xml", "dest": "/sitemap" }
  ]
} 
```

## 缩放

随着您的站点的增长，可能需要一些时间来构建和提供此文件。我喜欢使用像 cloudflare 这样的服务，它的缓存可以缓解这种情况。到目前为止，我还没有遇到任何速度陷阱，但我知道在一个超级大的网站地图上，在某一点上把它分成不同路线的多个网站地图可能是一个好主意。

希望这能像帮助我一样帮助别人。
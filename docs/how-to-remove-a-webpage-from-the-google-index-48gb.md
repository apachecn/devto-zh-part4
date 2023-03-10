# 如何从谷歌索引中删除网页

> 原文：<https://dev.to/vorakl/how-to-remove-a-webpage-from-the-google-index-48gb>

重要的是要记住，搜索引擎会定期扫描网站，而扫描周期可能会因多种因素而异。一般来说，网站所有者并不能完全控制搜索引擎的行为，但是他们可以以指令的形式定义偏好。例如，这种指令允许将某些网页排除在搜索结果之外，或者阻止搜索引擎挖掘特定的路径。有两种方式来声明首选项:调整网站根目录中 [robots.txt](https://www.robotstxt.org/) 的参数，以及在网页的`<head>`块中 [HTML `<meta>`标记“robots”](https://developers.google.com/search/reference/robots_meta_tag)。

我最近需要将我的一个静态网站转移到另一个域。这变成了一项复杂的任务，因为我不能改变服务器端的配置，而 HTTP 请求的重定向只是故事的一部分。一旦所有用户都被重定向到一个新的位置，我必须启动并加快清理搜索结果的过程，这些搜索结果都是指向我的旧网站的链接。

从搜索索引中删除网页基本上有几种常见的方法:

*   完全删除一个页面，这样客户端将得到 [404 Not Found](https://tools.ietf.org/html/rfc2616#section-10.4.5) HTTP 响应。这显然不是我的情况，因为旧网站的反应是有效的和现有的网页
*   通过要求客户端输入凭据来限制对页面的访问。然后，服务器将发送 [401 未授权的](https://tools.ietf.org/html/rfc2616#section-10.4.2) HTTP 响应。这对我也不起作用，因为需要改变服务器端的配置
*   添加一个值为 [noindex](https://support.google.com/webmasters/answer/93710) 的 HTML `<meta>`标签*机器人*。这正是我所需要的，并且可以在客户端实现。

最后一种方法允许从 HTML 代码中为每页设置不同的首选项。也就是说，搜索引擎必须能够访问一个页面来阅读它并找到这个指令。这也意味着所有带有 *robots* meta 标签的网页都不应该被 robots.txt 文件屏蔽！

这个解决方案将展示从谷歌搜索结果中移除整个网站的几个步骤。

*   检查 *robots.txt* (如果存在的话)并确保搜索机器人被允许通过网站并阅读所有索引网页。该文件应该是空的或类似这样的(允许任何机器人读取网站上的任何网页):

```
User-agent: * 
Disallow: 
```

*   将*机器人* HTML `<meta>`标签添加到每个索引网页中带有“noindex，nofollow”值的`<head>`块中:

```
<meta name="robots" content="noindex, nofollow" /> 
```

*   创建一个 [sitemap.xml](https://www.sitemaps.org/) 文件，并用指向最近某个时间的`<lastmod>`部分定义所有索引网页。例如:

```
<urlset >
    <url>
        <loc>https://example.com/page1/</loc>
        <changefreq>daily</changefreq>
        <lastmod>2019-06-15</lastmod>
    </url>
    <url>
        <loc>https://example.com/page2/</loc>
        <changefreq>daily</changefreq>
        <lastmod>2019-06-15</lastmod>
    </url>
</urlset> 
```

*   [将这个 sitemap.xml 文件](https://www.sitemaps.org/protocol.html#submit_ping)提交给 Google，让它知道最近的变化。可以使用*卷曲*命令来完成:

```
curl -sSLf https://google.com/ping?sitemap=https%3A%2F%2Fexample.com%2Fsitemap.xml 
```

*   [为每个被索引的网页提交移除请求](https://www.google.com/webmasters/tools/removals)。一些链接(每个页面的 URL 尝试几次)可能需要几天时间才能被认为“过时”并有资格从索引中删除
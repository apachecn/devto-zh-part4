# 在 Hugo 中创建内容安全策略(CSP)

> 原文：<https://dev.to/jeremylikness/create-a-content-security-policy-csp-in-hugo-444p>

[![Create a Content Security Policy (CSP) in Hugo](img/0ad54a5694710fdecf314314072e1d7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HbYvzbhG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jeremylikness.com/blog/create-content-security-policy-csp-in-huimg/cspviolation.jpg)

一个[内容安全策略(CSP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) 有助于防止像跨站脚本(XSS)和数据注入这样的攻击。当您包含来自第三方站点的 JavaScript 时，可能会发生典型的攻击。如果来自`trusteddomain.com`的 JavaScript 不知何故遭到破坏，该脚本可能会被修改以从`untrusteddomain.com`加载数据(或向其发送数据)。CSP 将通过明确阻止来自您不信任的域的操作来防止这种情况。

下面是我从控制台捕捉到的一个违规示例。我信任 Google 提供广告，但不允许`eval`运行(这允许来自字符串的动态代码被评估和执行，而不是包含在文件中的静态代码)。某些广告试图使用这一功能，但被 CSP 阻止。如果所有的广告都试图这样做，我会简单地把它们全部删除或者换成另一个提供商。

<figure>[![CSP violation](img/0ad54a5694710fdecf314314072e1d7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HbYvzbhG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jeremylikness.com/blog/create-content-security-policy-csp-in-huimg/cspviolation.jpg) 

<figcaption>CSP 违规</figcaption>

</figure>

实现 CSP 的典型方式是提供一个名为`Content-Security-Policy`的 HTTP 头。大多数 web 服务器都可以配置成提供这种功能，一些静态托管服务如 [Netlify](https://www.netlify.com) 允许你指定一个特殊的文件，这个文件被解析成包含自定义的头。如果这些选项不可用，您可以用 meta 标签实现您的 CSP。这是我写这篇文章时我的博客的 CSP 政策。

```
<meta http-equiv="Content-Security-Policy" content="upgrade-insecure-requests; block-all-mixed-content; default-src 'self'; child-src 'self'; font-src 'self' https://use.fontawesome.com https://fonts.gstatic.com https://public.slidesharecdn.com disqus.com disquscdn.com *.disqus.com *.disquscdn.com; form-action 'self' https://syndication.twitter.com/ https://platform.twitter.com disqus.com disquscdn.com *.disqus.com *.disquscdn.com; frame-src 'self' https://cse.google.com https://player.vimeo.com www.youtube.com www.youtube-nocookie.com https://platform.twitter.com https://syndication.twitter.com jsfiddle.net www.instagram.com https://kuula.co https://www.slideshare.net https://www.google.com https://googleads.g.doubleclick.net disqus.com disquscdn.com *.disqus.com *.disquscdn.com; img-src 'self' https://jeremylikness.visualstudio.com https://www.google-analytics.com https://stats.g.doubleclick.net https://pagead2.googlesyndication.com *.google.com *.gstatic.com *.amazon-adsystem.com https://www.googleapis.com https://images-na.ssl-images-amazon.com https://platform.twitter.com *.twimg.com https://syndication.twitter.com data: https://files.kuula.io disqus.com disquscdn.com *.disqus.com *.disquscdn.com; object-src 'none'; style-src 'self' 'unsafe-inline' https://use.fontawesome.com https://fonts.googleapis.com https://www.google.com https://platform.twitter.com *.twimg.com disqus.com disquscdn.com *.disqus.com *.disquscdn.com; script-src 'self' 'unsafe-inline' https://platform.twitter.com https://cdn.syndication.twimg.com *.amazon-adsystem.com https://www.google-analytics.com cse.google.com https://www.google.com https://pagead2.googlesyndication.com https://adservice.google.com https://www.googletagservices.com jsfiddle.net www.instagram.com disqus.com disquscdn.com *.disqus.com *.disquscdn.com;"> 
```

如你所见，内容非常丰富。我最初试图用手维护它，但这很快变得难以操作。所以，我决定采用不同的方法:配置。在我的网站的`config.toml`中，我为我的 CSP 添加了一个特殊的部分。

```
[params.csp]
childsrc = ["'self'"]
fontsrc = ["'self'"]
formaction = ["'self'"]
framesrc = ["'self'"]
imgsrc = ["'self'"]
objectsrc = ["'none'"]
stylesrc = ["'self'"]
scriptsrc = ["'self'"] 
```

这是我开始做的，但几乎没有成功，因为我依赖第三方网站的内容(例如，如果我想嵌入推文，我需要访问 Twitter)。

首先，让我们来划分类别:

*   **child-src** 为 web workers 和嵌套脚本配置行为(即在`iframe`标签中)
*   **font-src** 配置字体的加载位置
*   **表单动作**限制表单数据提交的位置
*   **frame-src** 配置哪些域可以向`iframe`标签提供内容
*   **img-src** 配置图像源
*   **object-src** 配置插件
*   **style-src** 配置样式表
*   **script-src** 配置 JavaScript 行为

值`none`禁止任何事情发生。我不允许我的网站上有任何插件。值`self`只允许 CSP 所在的域提供资源。打开 CSP 后，您有几个选项来监控它。异常将显示在控制台中。您还可以配置一个[报告端点](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)，指示浏览器向 HTTPS 端点发送信息。这对于从您部署的网站收集和跟踪信息非常有用。

我只是访问各种页面，并纠正出现的违规行为。例如，要嵌入 YouTube 视频，我需要允许`frame-src`访问*youtube.com*或*youtube-nocookie.com*的域名(后者允许我在不追踪你的用户数据的情况下播放视频)。我使用 Disqus 进行评论，这需要脚本，表格和其他访问。最终我建立了一个域名列表。这里是 JavaScript 的完整列表:

```
[params.csp]
scriptsrc = ["'self'",
"'unsafe-inline'",
"https://platform.twitter.com",
"https://cdn.syndication.twimg.com",
"*.amazon-adsystem.com",
"https://www.google-analytics.com",
"cse.google.com",
"https://www.google.com",
"https://pagead2.googlesyndication.com",
"https://adservice.google.com",
"https://www.googletagservices.com",
"jsfiddle.net",
"www.instagram.com",
"disqus.com",
"disquscdn.com",
"*.disqus.com",
"*.disquscdn.com"] 
```

`unsafe-inline`允许使用嵌入在`<script>`标签中的 JavaScript(而不是从外部文件加载)。事实上，I *不*包含`unsafe-eval`意味着不允许从动态字符串执行 JavaScript。`unsafe-inline`的一个替代方案是使用一种特殊的`sha256-hash`方法。违规将显示内联脚本的散列，然后您可以将该散列添加到 CSP 中，只要内联脚本没有改变，它就将被允许。

为了从配置文件中呈现 CSP，我在`/partials/shared`下创建了一个名为`CSP.html`的局部模板。代码看起来像这样:

```
printf `<meta http-equiv="Content-Security-Policy" content="upgrade-insecure-requests; block-all-mixed-content; default-src 'self'; ...` 
```

这是第一部分。我将所有 HTTP 请求升级到 HTTPS，因此如果第三方供应商试图获取没有 SSL 的东西，它会自动转换为安全请求。虽然多余，我也清楚我不支持混合内容(HTTP 和 HTTPS 在同一个页面)。默认情况下，任何项目都可以从我的域提供。单个保单如下所示内嵌打印:

```
printf `... child-src %s; font-src %s ...`
(delimit .Site.Params.csp.childsrc " ")
(delimit .Site.Params.csp.fontsrc " ")
| safeHTML 
```

`%s`是占位符。对于每个策略，我从配置(`.Site.Params.csp`)中取出列表，并使用空格作为分隔符将其折叠成一个字符串。这就把`["'self'", "'unsafe-inline'", "www.google.com"]`变成了`"'self' 'unsafe-inline' www.google.com"`。默认情况下，生成的字符串是 HTML 编码的。`| safeHTML`表示我信任 HTML，它在呈现之前不必被编码/转义。

最后一步是在我的标题顶部包含部分模板。在`partials/_shared`我把它加到了最上面:

```
<head>
<meta charset="utf-8">
{{ partialCached "_shared/csp.html" . }} 
```

我用`partialCached`来加速渲染。它只需对整个网站评估一次，然后用它生成每个页面。

现在，每当我需要进行更改时，我只需调整配置，就大功告成了。有了 CSP，我感觉更安全了，你也应该如此。

问候，

[![Jeremy Likness](img/77962a58337e1b2f5b401a24d772f320.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S1KFS2BY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.jeremylikness.cimg/jeremylikness.gif)
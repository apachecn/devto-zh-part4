# Kentico CMS 快速提示:自动静态文件指纹

> 原文：<https://dev.to/wiredviews/kentico-cms-quick-tip-automatic-static-file-fingerprinting-30c5>

<figure>

[![Wooden Finger](img/05364b1aae3eb657b5964e4ea08219f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8SMl-wrF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pgh2t77bdtv1kspojewm.jpg)

<figcaption>Photo by [Charles 🇵🇭](https://unsplash.com/@charlesdeluvio) on [Unsplash](https://unsplash.com)</figcaption>

</figure>

## 静态内容和网址

无论我们是在做一个 [Kentico CMS 门户引擎](https://docs.kentico.com/k12sp/developing-websites/portal-engine-development-overview)还是 [Kentico MVC](https://docs.kentico.com/k12sp/developing-websites/mvc-development-overview) 网站，我们可能都有一些静态内容由 IIS 提供。

这是没有存储在媒体库中或作为页面附件的内容——这些文件由运行的 ASP.NET 应用程序(CMS 或 MVC)提供服务。

我在这里主要讨论的文件是 CSS 和 JavaScript 文件，每次浏览器从我们的站点加载页面时都会请求这些文件。

虽然这些文件可以从媒体库或页面附件中提供，但更常见的是将它们与您编译的应用程序代码一起直接部署到 web 服务器文件系统中。

然后我们让 [IIS 的静态文件处理](https://stackoverflow.com/a/14270140/939634)为它们服务😏。

这些文件的 URL 看起来像什么🤔？

它们可以简单到文件的路径:

*   `/css/styles.css`

我们还可以在路径中包含构建信息:

*   `/dist/css/styles.min.css`

或者，我们可以在路径或查询字符串中包含版本号:

*   `/css/1.3.2/styles.css`
*   `/css/styles.css?v=1.3.2`

第一个选项似乎是最简单的，那么为什么不使用它，并继续解决我们应用程序中的实际问题呢🤷‍♀️?

* * *

## 客户端缓存

如果我们试图让我们的网站发挥最佳性能，拥有顶级的 SEO 排名，并在桌面和移动设备上拥有出色的 UX，那么我们可能希望利用缓存😉。

在以前的帖子中，我曾经写过关于在应用程序中进行缓存以加快数据访问的内容:

[![seangwright](img/e9e13f3e057a4f58b01316c7a9d6eeea.png)](/seangwright) [## Kentico 12:设计模式第 12 部分——数据库查询缓存模式

### 肖恩·g·赖特 8 月 26 日 1913 分钟阅读

#kentico #mvc #caching #csharp](/seangwright/kentico-12-design-patterns-part-12-database-query-caching-patterns-43hc)

然而，我现在提到的缓存是客户端缓存。

每次浏览器从我们的站点请求一个页面，并且这个页面有一个指向`/css/styles.css`的`<link>`标签，浏览器也会请求这个样式表。

通过在来自服务器的 HTTP 响应中使用正确的[缓存头，当`/css/styles.css`被请求时，我们可以指示浏览器缓存请求的结果，这在下次需要文件时节省了时间和带宽👍。](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching)

那么这一切是如何与静态文件的 URL 联系起来的呢？

### 处理客户端缓存

如果我们告诉客户端(浏览器)缓存对`/css/styles.css`的请求结果，那么在缓存过期之前，该浏览器不会对该文件发出额外的请求——浏览器已经拥有了它。

> 这个缓存过期时间将基于我们的服务器为所请求的文件发送的响应中的 HTTP 头。

如果我们在服务器上更新`/css/styles.css`，因为我们意识到在我们的 CSS 规则或设计中有一个错误，会发生什么？

没有人会快乐😑如果我们告诉他们，这个问题的修复将不会出现在网站访问者的浏览器中，直到他们的本地缓存过期。

所以，这就是上面提到的不同 URL 模式发挥作用的地方。

通过在 URL 中包含类似于文件版本的内容，我们可以在我们的修复上传到服务器后立即强制浏览器下载该文件。

为什么会这样？

### 使用版本控制

假设浏览器已经缓存了`/css/1.3.2/styles.css`，我们当前的页面有一个`<link>`标签，如下:

```
<link rel="stylesheet" href="/css/1.3.2/styles.css"> 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们用一些新的 CSS 更新文件，并将其部署到服务器上的`/css/1.4.0/styles.css`。

我们还需要更新`<link>`标签，可能在我们的母版页或`_Layout.cshtml`中，更新到下面:

```
<link rel="stylesheet" href="/css/1.4.0/styles.css"> 
```

Enter fullscreen mode Exit fullscreen mode

下次浏览器请求页面时，会看到对新 CSS 文件路径的引用(`/css/1.4.0/styles.css`)，发现这个文件不在本地缓存中，然后下载它并添加到缓存中😉。

问题是，这需要几个运动部件才能正常工作:

*   ❌:我们的 CSS 文件需要在每次更新时部署到文件系统上的新路径，这使得版本控制或 CI/CD 变得复杂。
*   ❌我们的`<link>`标签都需要更新到正确的路径，否则他们将提供一个过时或不存在的文件。
*   ❌:我们不仅要对所有的 CSS 文件这样做，还要对所有的 JavaScript 文件这样做。

如果有一种自动化的方式来做所有这些事情，那不是很好吗？🤨

* * *

## 自动 URL 版本控制(指纹识别)

几年前，Mads Kristensen 写了一篇博客文章，详细介绍了一种将版本信息插入静态文件 URL 的巧妙方法。

这种方法对于分布式(即 Web Farm)环境来说是简单、高效和安全的。

为此，我们需要在应用程序中添加 3 个部分:

*   ✅创建了一个助手方法来生成和缓存静态文件 URL。
*   ✅在我们所有引用静态文件的标记中使用这个辅助方法。
*   ✅将 URL 重写添加到我们的`web.config`中，这样 IIS 可以提供正确的文件。

让我们看看下面的每一步。

### 指纹助手法

我们可以在自定义的静态类(Web Forms)中编写这个 helper 方法，也可以将其作为`UrlHelper` (MVC)的扩展方法。我将假设 MVC 向前发展(关于 Web 表单场景，请参阅 Mads 的博客)。

```
/// <summary>
/// Inserts a fingerprint path for cache creation/breaking based on the file write time
/// </summary>
/// <param name="_"></param>
/// <param name="rootRelativePath"></param>
/// <returns></returns>
public static string Fingerprint(this UrlHelper _, string rootRelativePath)
{
    if (HttpContext.Current.Kentico().Preview().Enabled)
    {
        return rootRelativePath;
    }

    if (HttpRuntime.Cache[rootRelativePath] is null)
    {
        string absolute = HostingEnvironment.MapPath($"~{rootRelativePath}");

        var date = File.GetLastWriteTime(absolute);
        int index = rootRelativePath.LastIndexOf('/');

        string result = rootRelativePath.Insert(index, $"/v-{date.Ticks}");

        HttpRuntime
            .Cache
            .Insert(rootRelativePath, result, new CacheDependency(absolute));
    }

    return HttpRuntime.Cache[rootRelativePath] as string;
} 
```

Enter fullscreen mode Exit fullscreen mode

关于上面的代码，有一些事情需要注意。

首先，我们接受静态文件的根相对路径作为参数(例如:`/css/styles.css`)。这将是我们在 Razor 文件中呈现的普通 URL。

然后，我们检查请求上下文是否处于 Kentico 的 MVC 预览模式，如果是，我们短路禁用这个 URL 版本控制，这会破坏 CMS 中的预览模式/页面生成器功能😮。

如果我们在应用程序缓存中找不到某个项目，使用提供的 URL 作为关键字，我们在文件系统上创建该文件的绝对路径，找出它的最后修改日期，并将其插入到最后一个路径段之前。

这将导致以下 URL 更改:

在
之前

```
/css/styles.css 
```

Enter fullscreen mode Exit fullscreen mode

在
之后

```
/css/v-637034589309537495/styles.css 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将这个新的 URL 存储在缓存中，这样我们就不必在下一次页面请求时重新计算它，并返回缓存的值😎。

> 我们还依赖于绝对文件路径。
> 
> 如果该文件被更改，缓存的 URL 将被清除，我们将生成一个新的 URL，其中包含新的修改日期值。

### 在我们的 Razor 视图中生成 URL

我们可以在视图中利用上述方法，如下所示:

```
<link href="@Url.Fingerprint("/css/styles.css")" rel="stylesheet" /> 
```

Enter fullscreen mode Exit fullscreen mode

非常简单😋！

当然，我们可以将`@Url.Fingerprint()`用于我们站点上的任何静态文件，而不仅仅是 JavaScript 和 CSS。

### Web.config 网址重写

最后，在我们的`web.config`中，我们添加了下面的重写规则

```
<system.webServer>
  <rewrite>
    <rules>
      <rule name="Fingerprint-Rewrite">
        <match url="([\S]+)(/v-[0-9]+/)([\S]+)"/>
        <action type="Rewrite" url="{R:1}/{R:3}"/>
      </rule>
  </rewrite>
</system.webServer> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

这条规则规定，每当收到一个类似于`.../v-<some numbers>/...`的 URL 请求时，从与该 URL 匹配的文件系统路径中提供该文件，但是删除了`/v-<some numbers>`部分。

这个重写的路径是我们的文件的位置，所以文件每次都会被正确地提供🤓！

> *注意*:这个`v-<some numbers>`值是什么并不重要——它不需要精确，只需要在我们每次对文件进行修改时是唯一的，以迫使浏览器请求文件的新版本。
> 
> 对`/css/v-1234/styles.css`的请求将导致相同的文件作为对`/css/v-9876/styles.css`的请求。

* * *

## 最后的想法

如果需要，我们可以使用这个`Fingerprint()`方法来改变生成的 URL 的域，这样我们的文件就可以从一个单独的 CDN 域提供服务。

我们还可以根据环境切换这种重写和指纹识别——在本地/开发中禁用它，但在登台/生产中启用它。

记住，要真正利用这种方法带给我们的好处，我们需要确保为这些文件指定了正确的缓存头🙂。

客户端缓存是性能问题的解决方案，指纹是缓存有效性问题的解决方案。

> 查看关于缓存头的 [MDN 文章，确保您的应用程序配置正确。](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching)

感谢阅读，希望这篇文章对你有所帮助🙏。

* * *

如果您正在寻找更多的 Kentico 内容，请在 DEV:

## # [肯蒂科](https://dev.to/t/kentico) <button name="button" type="button" data-info="{&quot;className&quot;:&quot;Tag&quot;,&quot;style&quot;:&quot;full&quot;,&quot;id&quot;:5339,&quot;name&quot;:&quot;kentico&quot;}" class="crayons-btn follow-action-button whitespace-nowrap c-btn--secondary fs-base " aria-label="Follow tag: kentico" aria-pressed="false">跟随</button>

或者我的 Kentico 博客系列:

*   [Kentico 12:设计模式](https://dev.to/search?q=Kentico%2012%3A%20Design%20Patterns)
*   [Kentico CMS 快速提示](https://dev.to/search?q=Kentico%20CMS%20Quick%20Tip)
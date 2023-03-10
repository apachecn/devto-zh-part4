# 如何避免广告拦截器错误

> 原文：<https://dev.to/trackjs/how-to-avoid-ad-blocker-errors-495b>

网络上充斥着广告，许多用户安装广告拦截器扩展来对付它们。但是广告拦截器扩展可以以奇怪和意想不到的方式破坏网站，这通常表现为 TrackJS 中的错误。

与广告拦截器相关的错误在我们的客户中很常见。因此，在我自己最近调试了一些与广告拦截器相关的问题后，我想我应该分享 **3 个技巧来避免广告拦截器错误**。

够刺激了吗？

## 提示 1:不要指望数据分析会奏效

分析和社交网络脚本经常被广告拦截器扩展阻止。像*谷歌分析*、*脸书像素*、 *Mixpanel* 和 *Twitter 小工具*都被广告拦截器捕获并移除。

```
<strong>Don't count on external scripts to load.</strong> 
```

Enter fullscreen mode Exit fullscreen mode

例如，当一个广告拦截器屏蔽了*谷歌分析*，`ga`根本就不会存在。如果您试图从您的自定义 JavaScript 点击处理程序中调用`ga`，它将会中断:

```
Uncaught ReferenceError: ga is not defined 
```

Enter fullscreen mode Exit fullscreen mode

该错误会停止执行，并且您的 click 处理程序无法完成。广告拦截器无意中破坏了您的页面。

在编写第一个 TrackJS 注册表单时，我可能就遇到了这个问题。

每次引用从外部脚本加载的函数时，都应该进行安全检查，确保它存在。您可以使用一些检查变量“真实性”的简单 JavaScript 来做到这一点:

```
window.ga && ga("event", "funnel", "sign_up") 
```

Enter fullscreen mode Exit fullscreen mode

这种简单的检查可以防止外部脚本失败，无论是广告拦截器、网络故障还是第三方更改。

## 秘诀 2:避免与广告相关的词汇

广告拦截者实际上并不知道什么是“广告”。他们只是试图将 HTML 和网络请求与模式列表进行匹配。在您的代码中避免这些模式将有助于您避开广告拦截器的问题。

当你的图片、类名或 URL 包含其中一种模式时，它会被当作广告一样屏蔽掉。这不仅仅是避免使用“广告”这个词，这里有一些经常被屏蔽的示例模式:

*   *img/myImage-900x300.jpg`** 文件名中包含常见广告尺寸的图像。

*   **`/api/track`** 包含`track`、`pixel`或`ad`作为片段的 URL。这一个捕捉轨道的东西很多。

*   **`<div class="sponsor align-right">`** 在元素类名中使用类似`sponsor`或`ad`的关键字。

给事物命名很难。避免这些常见的模式会让你以后不再心痛。

## 提示 3:在本地运行广告拦截器

无论您采取何种预防措施，广告拦截器仍然会在您的网站上引起微妙的问题。[根据一项调查，超过 25%的互联网用户使用广告拦截器](https://www.statista.com/statistics/804008/ad-blocking-reach-usage-us/)，所以了解他们在你的网站上会有什么体验真的很重要。

最好的方法是自己运行一个广告拦截器来测试你的页面。我个人推荐 [Chrome](https://chrome.google.com/webstore/detail/ublock-origin/cjpalhdlnbpafiamejdnhcphjbkeiagm?hl=en) 和 [Firefox](https://addons.mozilla.org/en-US/firefox/addon/ublock-origin/) 上的 uBlock Origin。我用默认的“EasyList”过滤器列表配置了我的过滤器列表，这是最常用的。

如果你真的想大胆地发现你的网站会以新的和可怕的方式崩溃，打开“Fanboy 的增强跟踪列表”。这是一个激进的列表，阻止任何看起来可能会记得你的东西。不要担心如果你的网站崩溃了，大部分的网站都会崩溃。

如果你以前从未用广告拦截器浏览过你的网站，你可能有一两个问题需要解决。[前端错误监控](https://trackjs.com/)也有助于暴露问题，因为您的广告拦截器的行为可能与您用户的广告拦截器略有不同。理解真实用户的行为会告诉你影响用户的错误在哪里。
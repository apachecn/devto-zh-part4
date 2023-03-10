# 公开 CORS 响应的标头

> 原文：<https://dev.to/chromiumdev/exposing-headers-on-cors-responses-1k8j>

## CORS 及其不满

在我的职业生涯中, [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 要求的概念出现了很多次。很多时候，它是关于为什么给定的响应是[不透明的](https://stackoverflow.com/questions/39109789/what-limitations-apply-to-opaque-responses)，以及如何使该响应非不透明，以便它与服务工作者和缓存 API 配合得很好。

幸运的是，如今许多流行的第三方 API 和主机都支持 CORS，解决与 CORS 相关的基本问题通常可以归结为，比如说，在你的`<img>`标签中添加 [`crossorigin`属性](https://developer.mozilla.org/en-US/docs/Web/HTML/CORS_settings_attributes)。

## 当 CORS 还不够

但是，尽管启用 CORS 足以获取 HTTP 响应的基本信息，如状态代码或对其主体的访问，但仍有一些信息在默认情况下被锁定。例如，暴露在 CORS 响应上的头限于以下六个[“简单”响应头](https://developer.mozilla.org/en-US/docs/Glossary/Simple_response_header):

*   `Cache-Control`
*   `Content-Language`
*   `Content-Type`
*   `Expires`
*   `Last-Modified`
*   `Pragma`

当在服务人员内部访问时，其中一些头文件可能会派上用场，但是有一个头文件特别有用，但在默认情况下并不公开:`Date`。

特别是，如果您正在使用[工作箱的缓存过期](https://developers.google.com/web/tools/workbox/modules/workbox-cache-expiration#restrict_the_age_of_cached_entries)逻辑，并且您提供了一个`maxAgeSeconds`参数，那么缓存响应[的`Date`将根据当前时间和`maxAgeSeconds`之间的差异进行检查](https://github.com/GoogleChrome/workbox/blob/b0825d74d81264e7b4537ed170dd60de638561ba/packages/workbox-expiration/src/Plugin.ts#L176-L195)。如果`Date`太旧，那么缓存的响应将被忽略。

但是...只有当响应中暴露了一个`Date`头时，这个逻辑才有效。默认情况下，CORS 的反应不会是这样。

## 展览

像许多与 CORS 相关的事情一样，解决方法包括摆弄 HTTP 响应头。您要么需要自己访问底层 HTTP 服务器，要么需要联系您的 CDN/API 提供商，请求他们做出更改。

设置 [`Access-Control-Expose-Headers: Date`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Expose-Headers) 将允许`Date`响应头对您的 web 应用程序的代码可见，并且您可以在逗号分隔的列表中包含任何附加的头。

如果你正在使用你自己的基于 Express 的 web 服务器，那么 [`corser`中间件](https://www.npmjs.com/package/corser)看起来非常适合建立一个工作配置。他们的文档包括一个[配方](https://www.npmjs.com/package/corser#getting-a-response-header-returns-refused-to-get-unsafe-header-x)，用于配置暴露的响应头。
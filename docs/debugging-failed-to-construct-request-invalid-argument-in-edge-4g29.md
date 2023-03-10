# 调试:“未能构造‘请求’:参数无效。”在边缘

> 原文：<https://dev.to/trackjs/debugging-failed-to-construct-request-invalid-argument-in-edge-4g29>

您的代码没有任何变化。突然间，微软 Edge 用户开始出现大量错误。到底发生了什么事？

2019 年 8 月 28 日，许多 TrackJS 客户看到微软 Edge 浏览器的错误突然激增:`Failed to construct 'Request': Invalid Argument.`

引发这一错误的导火索是脸书软件开发工具包的一项变化。具体来说，这段代码来自他们的`sdk.js` :

```
fetch(url, { 
  referrerPolicy:"origin", 
  mode:"cors", 
  credentials:"include" 
}); 
```

在这个对 [`fetch`](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Syntax) 的呼唤中，脸书正在传递`referrerPolicy`。然而，正如在[兼容性表](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Browser_compatibility)中指出的那样，`referrerPolicy`对微软 Edge“不支持”。

“不支持”是一种轻描淡写的说法。

## 根本原因

`fetch`可以用一个 [`Request`](https://developer.mozilla.org/en-US/docs/Web/API/Request) 的物体来称呼。然而，所有已知版本的微软 Edge，通过 18.18975，这将抛出一个错误，如果`referrerPolicy`被作为一个选项。

例如，这段代码会在 Edge 中抛出一个错误:

```
var req = new Request("https://example.com/", { referrerPolicy: "origin" }); 
```

`Failed to construct 'Request': Invalid Argument.`

`referrerPolicy`属性是 2018 年 6 月加入标准的，所以 Edge 还不支持也是可以理解的。不过 Edge 抛出的事实说明微软对`Request`的实现正在泄露。

关于这个问题，我们已经向微软提出了[个问题。然而，由于他们将很快](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/23391036/)[切换到铬发动机](https://www.zdnet.com/article/heres-microsofts-updated-roadmap-for-chromium-based-edge-features-for-the-enterprise/)，我们预计这将无法解决。

## 工作区

[ReferrerPolicy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy) 指定当前的 URL 是否将在请求的`Referer`头中提供。 [MDN 有一些很好的例子](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy#Examples)展示了如何应用这一点。

虽然脸书需要在他们的 sdk 上解决这个问题，但在您自己的代码中可能仍然会出现这个错误。幸运的是，该策略最常见的用例仍然可以实现。

### 选项 1:不使用`Request`

这个问题只在使用`Request`对象作为`fetch`的参数时才表现出来。你可以将同样的参数直接传递给`fetch`。例如，

```
fetch(new Request('/', {/*options*/}))
// becomes
fetch('/', {/*options*/}) 
```

### 选项#2:使用默认`referrerPolicy`

不要为请求设置`referrerPolicy`。这将默认为页面的策略，并发送适当的信息。在为您的网页提供服务时，您需要适当地设置`referrerPolicy`头。

### 选项#3:显式设置引用

您可以控制多少信息与 [`referrer`](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Syntax) 属性一起传递，而不是依赖策略来决定发送什么作为原始值。

例如，如果您想要复制`"origin"`referrer policy，您可以使用以下代码，通过将 referrer 设置为您的域的根来从 referer 中去除路径信息。

```
fetch("https://example.com/", { referrer: "/" }); 
```

另一个例子，如果你想复制`"no-referrer"` referrerPolicy，你可以通过将它设置为空字符串来移除 referrer。

```
fetch("https://example.com/", { referrer: "" }); 
```

* * *

浏览器和第三方可能会在你最意想不到的时候在你的网站上制造错误。TrackJS 的生产错误监控会让你知道什么时候有人破坏了你的网站。试试看，让我们一起建立一个更好的网站。
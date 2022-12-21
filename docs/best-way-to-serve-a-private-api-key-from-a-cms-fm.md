# 从 CMS 提供私有 API 密钥的最佳方式

> 原文：<https://dev.to/bensegni/best-way-to-serve-a-private-api-key-from-a-cms-fm>

就一个小问题。

我有一个 angular web 应用程序，它使用 OneSignal 向移动应用程序发布通知。很好，但是有一个问题...

在 OneSignal 服务的报头中，我有一个来自 onesignal 的用于授权的私有 api 密钥，我知道这真的很糟糕...

我可以将私钥存储在我的 cms 上，这个 api 端点对公众是私有的。只有登录后才能访问。

有没有人能推荐一种好的方法来为服务带来价值，比如从一个组件或另一个服务，所以我将 httpOptions 设置为类似于。

const http options = {
headers:new http headers({
" Content-Type ":" application/JSON；charset=utf-8 "，
" Authorization ":value . from _ other _ component _ or _ service//这是来自服务器上端点的值。
})
}

抱歉，如果这不是提出这类问题的地方。这是我的第一篇文章，所以如果我把它放到了错误的地方，请指引我到正确的地方。

干杯。
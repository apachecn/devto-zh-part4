# 如何让 Nginx 缓存 cookie 感知？

> 原文：<https://dev.to/ale_ukr/how-to-make-nginx-cache-cookie-aware-2ffl>

在这篇文章中，我将描述如何配置 nginx 来缓存基于特定 cookie 值的响应。

让我们想象一种情况，当你想在你的网站上做一个 A/B 测试，50%的用户应该在页面上看到新的标题文本。其他 50%的访问者将继续看到旧页面。在这种情况下，你所有关于将用户拆分到不同版本的服务器端操作都会被 nginx 缓存忽略(当然，如果你有的话)。

这是因为 nginx 的默认配置是:

```
proxy_cache_key $scheme$proxy_host$request_uri; 
```

因此，它将对所有使用相同 url 请求页面的用户使用相同的缓存键。

幸运的是，nginx 允许我们轻松地定制 proxy_cache_key！我们需要做的，只是给这个键添加一个特定的 cookie:

```
proxy_cache_key $scheme$proxy_host$request_uri$cookie_MY_COOKIE_NAME; 
```

就是这样！在这之后，如果 userA 有 MY_COOKIE_NAME=A，userB 有 MY_COOKIE_NAME=B，他们将通过相同的 url 接收不同版本的页面。

如果你需要更复杂的行为，在 nginx 配置中不使用硬编码的 cookie 名称，你可以这样做:

```
if ($http_cookie ~* "ab_(.*?)=([\w-]+)" ) {
  set $abcookie $1$2;
}

proxy_cache_key $scheme$proxy_host$request_uri$abcookie; 
```

如您所见，您可以使用$http_cookie 并基于特定的 cookie 模式生成 proxy_cache_key。在这个具体的例子中，我们检查 http cookies 是否包含正则表达式模式为“ab_(.*?)=([\w-]+)"如果这个 cookie 存在，我们为 proxy_cache_key 生成新的变量。
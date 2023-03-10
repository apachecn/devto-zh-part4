# Nginx:关于 proxy_pass 的一切

> 原文：<https://dev.to/danielkun/nginx-everything-about-proxypass-2ona>

随着微服务的出现，入口路由和服务之间的路由成为日益增长的需求。我目前默认使用 [nginx](https://www.nginx.com) 来做这个决定——没有合理的理由或经验来支持这个决定，只是因为它似乎是目前使用最多的工具。

然而，经常需要的`proxy_pass`指令让我抓狂，因为它对我来说是不直观的行为。所以我决定记下它是如何工作的，用它能做什么，以及如何避开它的一些怪癖。

# 首先，https 上的一个说明

默认情况下，如果端点是 https,`proxy_pass`不验证端点的证书(这怎么会是默认行为呢？！).这在内部可能是有用的，但通常您希望非常明确地这样做。如果你使用公共路由的端点，我过去就这么做过，确保将`proxy_ssl_verify`设置为`on`。您还可以使用客户端证书对上游服务器进行身份验证，请确保在[https://docs . nginx . com/nginx/admin-guide/security-controls/securing-http-traffic-upstream/](https://docs.nginx.com/nginx/admin-guide/security-controls/securing-http-traffic-upstream/)查看可用选项。

# 一个简单的例子

当有一个 nginx 实例处理许多事情，并将其中一些请求委托给其他服务器时，通常会使用`proxy_pass`。一些例子是 Kubernetes 集群中的入口，它在负责特定位置的不同微服务之间传播请求。或者，你可以使用 nginx 直接为前端提供静态文件，而一些服务器端呈现的内容或 API 由 WebApp(如 ASP.NET 核心或 flask)提供。

让我们假设我们有一个运行在 [http://localhost:5000](http://localhost:5000) 上的 WebApp，并希望它在[http://localhost:8080/web app/](http://localhost:8080/webapp/)上可用，下面是我们如何在一个最小的 nginx.conf:
中实现它

```
daemon off;
events {
}
http {
    server {
        listen 8080;
        location /webapp/ {
            proxy_pass http://127.0.0.1:5000/api/;
        }
    }
} 
```

您可以将它保存到一个文件中，例如 nginx.conf，并使用

`nginx -c $(pwd)/nginx.conf`。

现在，您可以访问[http://localhost:8080/web app/](http://localhost:8080/webapp/)，所有请求将被转发到[http://localhost:5000/API/](http://localhost:5000/api/)。
注意/webapp/前缀是如何被 nginx“切掉”的。这就是位置的工作方式:它们切断`location`规范中指定的部分，并将其余部分传递给“上游”。“上游”被称为 nginx 后面的任何东西。

# 要斜线还是不要斜线

除了在`proxy_pass`上游定义中使用变量，我们将在下面了解到，位置和上游定义非常简单地联系在一起。这就是为什么你需要注意斜线，因为当你没有得到正确的时候，一些奇怪的事情会发生。

这里有一个方便的表格，向您展示了 WebApp 将如何接收请求，这取决于您如何编写`location`和`proxy_pass`声明。假设所有请求都发送到

| 位置 | 代理 _ 通行证 | 请求 | 由上游接收 |
| --- | --- | --- | --- |
| /webapp/ | [http://localhost:5000/API/](http://localhost:5000/api/) | /webapp/foo？bar=baz | /api/foo？bar=baz |
| /webapp/ | [http://localhost:5000/API](http://localhost:5000/api) | /webapp/foo？bar=baz | /apifoo？bar=baz |
| /webapp | [http://localhost:5000/API/](http://localhost:5000/api/) | /webapp/foo？bar=baz | /api//foo？bar=baz |
| /webapp | [http://localhost:5000/API](http://localhost:5000/api) | /webapp/foo？bar=baz | /api/foo？bar=baz |
| /webapp | [http://localhost:5000/API](http://localhost:5000/api) | 网络应用程序？条=基 | /apifoo？bar=baz |

换句话说:你通常总是想要一个尾随斜线，永远不要混合使用和不使用尾随斜线，只有当你想要将某个路径组件连接在一起时才想要没有尾随斜线的(我猜这种情况很少发生)。请注意查询参数是如何保留的！

# $uri 和$request_uri

你必须想办法避免`location`被切断:首先，你可以简单地重复`proxy_pass`定义中的位置，这很容易:

```
location /webapp/ {
    proxy_pass http://127.0.0.1:5000/api/webapp/;
} 
```

这样你的上游 WebApp 会收到/api/webapp/foo？上面例子中的 bar=baz。

重复位置的另一种方法是使用$uri 或$request_uri。区别在于$request_uri 保留了查询参数，而$uri 丢弃了它们:

| 位置 | 代理 _ 通行证 | 请求 | 由上游接收 |
| --- | --- | --- | --- |
| /webapp/ | [http://localhost:5000/API $ request _ uri](http://localhost:5000/api%24request_uri) | /webapp/foo？bar=baz | /api/webapp/foo？bar=baz |
| /webapp/ | [http://localhost:5000/API $ uri](http://localhost:5000/api%24uri) | /webapp/foo？bar=baz | /api/webapp/foo |

注意在`proxy_pass`定义中,“api”和$request_uri 或$uri 之间没有斜线。这是因为完整的 URI 将总是包含一个前导斜杠，如果您编写“api/$uri”，这将导致一个双斜杠。

# 捕获正则表达式

虽然这不是`proxy_pass`独有的，但我发现使用正则表达式将请求的一部分转发给上游 WebApp 或者重新格式化它通常是很方便的。举例:你的公共 URI 应该是[http://localhost:8080/api/cart/items/123](http://localhost:8080/api/cart/items/123)，你的上游 API 以[的形式处理 http://localhost:5000/cart_api？items=123](http://localhost:5000/cart_api?items=123) 。在这种情况下，或者更复杂的情况下，您可以使用 regex 捕获请求 uri 的一部分，并将其转换成所需的格式。

```
location ~ ^/api/cart/([a-z]*)/(.*)$ {
   proxy_pass http://127.0.0.1:5000/cart_api?$1=$2;
} 
```

# 使用带有 WebApp 的 try_files 作为后备

我遇到的一个用例是，我希望 nginx 处理一个文件夹中的所有静态文件，如果文件不可用，就将请求转发到后端。例如，通过 flask 交付的 Vue 单页应用程序(SPA)就是这种情况——因为主 HTML 需要一些服务器端调整——我想处理 nginx 静态文件而不是 flask。(这是官方 [gunicorn docs](http://docs.gunicorn.org/en/stable/deploy.html) 推荐的。)

在/app/wwwroot/上，您可能拥有除 index.html 之外的所有水疗设施，并且 [http://localhost:5000/](http://localhost:5000/) 将提供您的服务器调整 index.html。

你可以这样做:

```
location /spa/ {
   root /app/wwwroot/;
   try_files $uri @backend;
}
location @backend {
   proxy_pass http://127.0.0.1:5000;
} 
```

注意，由于某种原因，您不能在@backend 的`proxy_pass`指令中指定任何路径。Nginx 会告诉你:

`nginx: [emerg] "proxy_pass" cannot have URI part in location given by regular expression, or inside named location, or inside "if" statement, or inside "limit_except" block in /home/daniel/projects/nginx_blog/nginx.conf:28`

这就是为什么您的后端应该接收任何请求并返回它的 index.html，或者至少是由前端的路由器处理的路由的。

# 即使不是所有上游主机都可用，也让 nginx 启动

到目前为止，我使用 127.0.0.1 而不是 localhost 的一个原因是 nginx 对主机名解析非常挑剔。由于某些无法解释的原因，nginx 会在启动时尝试解析所有在`proxy_pass`指令中定义的主机，当它们不可达时无法启动。然而，特别是在微服务环境中，要求所有上游服务在入口、负载平衡器或一些中间路由器启动时可用是非常脆弱的。

您可以通过在`proxy_pass`指令中使用变量来规避 nginx 对所有主机在启动时可用的要求。然而，由于某种难以理解的原因，如果您这样做，您需要一个专用的`resolver`指令来解析这些路径。对于 Kubernetes，可以在这里使用 kube-dns.kube-system。对于其他环境，您可以使用内部 DNS，或者对于公共路由的上游服务，您甚至可以使用公共 DNS，如 1.1.1.1 或 8.8.8.8。

*另外*，使用`proxy_pass`中的变量完全改变了 URIs 传递到上游的方式。刚换
时

```
proxy_pass https://localhost:5000/api/; 
```

至

```
set $upstream https://localhost:5000;
proxy_pass $upstream/api/; 
```

...你可能认为结果应该完全一样，你可能会感到惊讶。前者将通过我们对`/webapp/foo?bar=baz`的示例请求，用`/api/foo?bar=baz`攻击您的上游服务器。然而，后者会用`/api/`打击你的上游服务器。没有 foo。没有酒吧。也没有巴兹。:-(

我们需要通过将两部分的请求放在一起来解决这个问题:首先是位置前缀后面的路径，其次是查询参数。第一部分可以用我们上面学过的正则表达式捕获，第二部分(查询参数)可以用内置变量`$is_args`和`$args`转发。如果我们把它们放在一起，我们将得到这样一个配置:

```
daemon off;
events {
}
http {
    server {
        access_log /dev/stdout;
        error_log /dev/stdout;
        listen 8080;
        # My home router in this case:
        resolver 192.168.178.1;
        location ~ ^/webapp/(.*)$ {
            # Use a variable so that localhost:5000 might be down while nginx starts:
            set $upstream http://localhost:5000;
            # Put together the upstream request path using the captured component after the location path, and the query parameters:
            proxy_pass $upstream/api/$1$is_args$args;
        }
    }
} 
```

虽然 localhost 在这里不是一个很好的例子，但是它也适用于您的服务的任意 DNS 名称。我发现这个*在生产中非常*有价值，因为让一个 nginx 因为一个可能非常不重要的服务而拒绝启动，在争论生产问题时可能是一件相当麻烦的事情。但是，这使得位置指令变得更加复杂。从一个简单的`location /webapp/`和一个`proxy_pass http://localhost/api/`变成了这个庞然大物。不过，我认为这是值得的。

# proxy _ pass 更好的日志格式

为了调试问题，或者只是为了在将来调查问题时手头有足够的信息，您可以最大化关于使用`proxy_pass`的`location`中正在发生什么的信息。

我发现这个很方便的`log_format`，我用一个自定义变量$upstream 增强了它，就像我们在上面定义的那样。如果您在所有使用`proxy_pass`的位置总是调用您的变量$upstream，您可以使用这个`log_format`，并且在您的日志中经常有非常需要的信息:

`log_format upstream_logging '[$time_local] $remote_addr - $remote_user - $server_name to: $upstream: $request upstream_response_time $upstream_response_time msec $msec request_time $request_time';`

下面是一个完整的例子:

```
daemon off;
events {
}
http {
    log_format upstream_logging '[$time_local] $remote_addr - $remote_user - $server_name to: "$upstream": "$request" upstream_response_time $upstream_response_time msec $msec request_time $request_time';
    server {
        listen 8080;

        location /webapp/ {
            access_log /dev/stdout upstream_logging;
            set $upstream http://127.0.0.1:5000/api/;
            proxy_pass $upstream;
        }
    }
} 
```

然而，我还没有找到一种方法来记录转发给$upstream 的实际 URI，这是调试`proxy_pass`问题时需要知道的最重要的事情之一。

# 结论

我希望您在这篇文章中找到了有用的信息，可以在开发和生产 nginx 配置时很好地利用这些信息。
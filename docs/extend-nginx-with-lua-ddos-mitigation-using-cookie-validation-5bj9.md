# 使用 Cookie 验证扩展 NGINX 的 Lua-DDOS 缓解

> 原文：<https://dev.to/satrobit/extend-nginx-with-lua-ddos-mitigation-using-cookie-validation-5bj9>

### 简介

“OpenResty 是一个成熟的网络平台，集成了我们增强版的 [Nginx](https://openresty.org/en/nginx.html) 内核、我们增强版的 [LuaJIT](https://github.com/openresty/luajit2) 、许多精心编写的 Lua 库、许多高质量的[第三方 Nginx 模块](https://openresty.org/en/components.html)，以及它们的大部分外部依赖项。它旨在帮助开发人员轻松构建可扩展的 web 应用程序、web 服务和动态 web 网关。”

### 挑战

网络服务器运营商面临的挑战之一是检测真实用户的僵尸程序。其中之一是将令牌作为 cookie 发送，并要求客户端返回 cookie。

### 解

使用 OpenResty 实用程序可以非常容易地做到这一点。让我们从基础开始。你可能知道，NGINX 有一个链式模块架构。在请求的生命周期中有许多阶段。OpenResty 提供了对这些阶段的访问，因此我们可以使用心爱的 Lua 来改变 NGINX 的行为。

#### 阶段

<figure>[![](img/c00a08ef1900ab8702303e55bd1fbf0f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7aySW_GE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1005/1%2AgfAT3GULg89r8MivUVCjQg.jpeg)

<figcaption>OpenResty 中请求生命周期的不同阶段</figcaption>

</figure>

我们将在 rewrite_by_lua 阶段运行。

#### 开放式基础知识

为了更好地理解如何使用 OpenResty，我建议您参考“[如何在 Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-use-the-openresty-web-framework-for-nginx-on-ubuntu-16-04) 上使用 OpenResty Web Framework for Nginx”，这是一篇非常适合初学者的文章。

OpenResty 在 NGINX 配置文件中定义了多个指令，因此我们可以将 Lua 代码放入其中。请看这个例子:

```
server {
    . . .

    location /example {
         default_type 'text/plain';

         content_by_lua_block {
             ngx.say('Hello, Sammy!')
         }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

content_by_lua 基本上允许我们操纵返回给客户端的内容。由于我们不需要提供任何内容，我们将使用 rewrite_by_lua 指令，该指令允许我们进入重写阶段。

好了，让我们回到我们的模块，这是一个简单的用于 ddos 缓解的 cookie 验证。

### 模块流程

#### Cookie 查找

我们模块的流程将保持简单和愚蠢。我们做的第一件事是检查名为“Ref”的 cookie。对于 cookie 操作，我使用了一个名为 [lua-resty-cookie](https://github.com/cloudflare/lua-resty-cookie) 的库。

要获取 cookie，我们只需让 cookie 对象调用 get 方法:

```
local cookie, err = ck:new()
if not cookie then
    ngx.log(ngx.ERR, err)
    return
end
local Ref_field, err = cookie:get("Ref")
if not Ref_field then
    -- No cookie for you
end 
```

Enter fullscreen mode Exit fullscreen mode

#### 返回 JWT

如果 cookie 不存在，我们使用 JWT 创建一个令牌，并将客户端的 IP 地址包含在有效负载中，然后返回一个 HTTP 代码 302，其中包含当前 URL 和 cookie。

对于 JWT，我使用了 [lua-resty-jwt](https://github.com/SkyLothar/lua-resty-jwt) 。你可以看到我们基本上创建了一个令牌，放入一个 cookie 并将其发送回客户端:

```
-- Make JWT
local jwt_token = jwt:sign(key,
{
    header={typ="JWT", alg="HS256"},
    payload={remote_addr=ngx.var.remote_addr}
})
-- set cookie
local ok, err = cookie:set({key = "Ref", value = jwt_token})
if not ok then
    ngx.log(ngx.ERR, err)
    return
end
-- Return to the same page with the Ref cookie included
return ngx.redirect(ngx.var.request_uri) 
```

Enter fullscreen mode Exit fullscreen mode

#### JWT 验证

当 302 命中客户机时，另一个请求进来，这次“Ref”cookie 也包含在请求中。现在我们有了 cookie，这个场景将与客户机一直有 cookie 的情况相同。

现在我们试着验证这个令牌:

```
-- Verify the Ref
local jwt_obj = jwt:verify(key, Ref_field)
if jwt_obj.verified ~= true or jwt_obj.payload.remote_addr ~= ngx.var.remote_addr then
    -- Create token and return it
end 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到，如果令牌验证失败，我们可以像客户端没有令牌一样重复这些步骤。

### Lua 模块

现在，我们用一些元数据将代码包装在一个表中，并返回该表。

这是我们的全部代码:
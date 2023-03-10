# HTTP 动词简体

> 原文：<https://dev.to/andeshchogle/http-verbs-simplified-5bib>

*交叉发布自:[https://www . andeshchogle . com/blog/post/http-verbs-simplified/](https://www.andeshchogle.com/blog/post/http-verbs-simplified/)*

#### 什么是 URL(或 URI)

URL 或统一资源定位器(URI 或统一资源标识符)是一种在网络上定位资源的方法。

例如:

> [http://www.contoso.com/users/1](http://www.contoso.com/users/1)

我们可以将上面的 URL 解释为:它标识一个“Id”为 1 的用户，该用户存在于“www.contoso.com”所指向的网络上的“users”集合中，并且可以通过“http”协议进行访问。

示例 2:

> [http://www.contoso.com/users](http://www.contoso.com/users)

上面的 URL 标识了“www.contoso.com”指向的网络上的“users”集合，并且可以通过“http”协议进行访问。

#### HTTP 扮演的角色

现在我们已经使用 URL 定位了一个资源，我们可以使用 HTTP 作为“对那个资源做各种事情”的协议对它做各种事情。HTTP 标准有一些我们可以使用的预定义的“[动词](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)”(或动作)。这些动词中的每一个都有一个预定义的行为，即:

1.  使用该动词(即执行该动作)需要什么输入
2.  当这个动作被执行时，我们应该期待什么样的回报
3.  如果我们在同一个资源上多次执行同一个动词，会发生什么
4.  如果 URL 是错误的，例如 URL 指向一个不存在的资源，会发生什么

下面是最常用的 HTTP 动词及其行为的列表。

#### 得到

顾名思义，在 URL 上执行 GET 可以获得该 URL 所指向的资源。执行 GET 的响应通常会有一个状态码 200(即“OK”，即请求被成功执行)，该响应的主体将是实际的资源。

```
GET /users/1 
```

在上面的例子中，我们将从“users”集合中获取一个 id 为 1 的用户。

如果 id 为 1 的用户不存在，或者如果用户集合本身不存在，请求将返回状态代码 404(未找到)。

按照标准，GET 请求是幂等的。简单地说，这意味着从服务器的角度来看，多次执行请求与只执行一次请求具有相同的效果。例如，如果我们不止一次地执行同一个 GET 请求，服务器应该保持相同的状态，就好像该请求只执行了一次一样。

#### 岗位

```
POST /users

{
    id: 1,
    firstName: "John",
    lastName: "Doe",
    age: 30,
    profilePic: "http://www.contoso.com/media/johndoe.jpg",
    address: {
        city: "Pune",
        state: "Maharashtra",
        country: "India"
    }
} 
```

在上面的例子中，我们对 URL“/users”执行一个 POST。请求体包含一个 JSON 格式的用户表示。在这个例子中，我们对服务器说，“接受这个资源作为/users 集合下的用户”。根据业务逻辑，服务器可能会在“用户”集合下创建一个用户对象，或者做一些类似的事情。

如果服务器创建用户，它应该返回 201(已创建)状态代码。如果服务器选择对发布的用户做其他事情，那么它可能返回 200 (OK)状态代码。

POST 请求既不需要也不期望是幂等的。这意味着如果我们不止一次地执行 POST 请求，每个请求都会对服务器产生影响。从服务器的角度来看，多次执行它并不等同于只执行一次。对于上面的 POST 请求，执行一次会创建一个用户，执行两次会创建一个重复的用户。

#### 放

```
PUT /users/1

{
    id: 1,
    firstName: "John",
    lastName: "Doe",
    age: 30,
    profilePic: "http://www.contoso.com/media/johndoe.jpg",
    address: {
        city: "Pune",
        state: "Maharashtra",
        country: "India"
    }
} 
```

在上面的 PUT 请求中，我们对服务器说，“获取这个用户资源并将其放在/users/1 位置”。

服务器应该将提供的资源放在“/users/1”位置。如果资源已经存在于“/users/1”位置，服务器应该将提供的用户资源视为资源的更新版本，并用新资源替换旧资源。

如果请求导致服务器创建新资源，服务器应该用 201(已创建)状态代码来响应。如果请求导致更新现有资源，服务器可能会回复一个 200 (OK)状态代码。

根据标准，PUT 请求应该是幂等的。这意味着多次执行相同的 PUT 请求将使服务器处于与只执行一次相同的状态，即只有一个用户位于/users/1

#### 删除

```
DELETE /users/1 
```

简单地说，删除将请求服务器删除指定位置的资源。在上面的示例中，删除请求将删除位于/users/1 的用户

如果服务器删除了资源，它应该用 200 (OK)状态码来响应；如果它已经接受了请求，但还没有删除资源，但以后会删除，则应该用 202(已接受)来响应。

删除也应该是幂等的。因此，多次执行删除请求“DELETE /users/1”将使服务器处于与只执行一次“DELETE /users/1”相同的状态，即/users/1 位置没有用户。

还有其他动词，如 HEAD、OPTIONS、PATCH 等。，但以上是最常用的四种。

为了更容易理解，这篇文章被简化了。学习这些动词最真实的地方是万维网联盟(W3C)网站本身。所以为了深入研究这个话题，你可能想去 http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html

#### 参考文献

RFC 2616，第 9 节:[http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)
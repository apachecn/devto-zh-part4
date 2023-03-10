# Go:创建 gRPC 拦截器

> 原文：<https://dev.to/davidsbond/golang-creating-grpc-interceptors-5el5>

### 简介

就像构建 HTTP APIs 时一样，有时您需要中间件来应用于 HTTP 处理程序，以进行请求验证、身份验证等。在 gRPC 中，这没有什么不同。身份验证方法需要以“全有或全无”的方式应用于服务器和客户端。对于外行来说，gRPC 将自己描述为:

> 一个现代的开源高性能 RPC 框架，可以在任何环境中运行。它可以通过对负载平衡、跟踪、运行状况检查和身份验证的可插拔支持，高效地连接数据中心内和跨数据中心的服务。它也适用于分布式计算的最后一英里，将设备、移动应用程序和浏览器连接到后端服务。

这里的关键区别在于，在 HTTP 中，我们为处理程序创建了中间件(纯粹在服务器端)。使用 gRPC，我们可以为服务器端的入站调用和客户端的出站调用创建中间件。这篇文章旨在概述如何创建简单的 gRPC 拦截器，作为客户机和服务器的中间件。

### 拦截器类型

gRPC 中有两种拦截器，**一元**和**流**。一元拦截器处理单个请求/响应 RPC 调用，而流拦截器处理消息流双向写入的 RPC 调用。你可以从[这里](https://grpc.io/docs/guides/concepts/#rpc-life-cycle)获得更多关于它们之间差异的细节。在此基础上，您可以创建适用于服务器和客户端的拦截器。

#### 一元客户端拦截器

在我们有一个简单的调用和响应的情况下，我们需要创建一个一元客户端拦截器。这是一个匹配`grpc.UnaryClientInterceptor`签名的函数，看起来像这样:

```
func Interceptor(ctx context.Context, method string, req, reply interface{}, cc *grpc.ClientConn, invoker grpc.UnaryInvoker, opts ...grpc.CallOption) error {
  // Do some things and invoke `invoker` to finish the request
} 
```

Enter fullscreen mode Exit fullscreen mode

这个签名有很多参数，所以让我们看看每个参数以及它们的用途:

*   `ctx context.Context` -这是请求上下文，主要用于超时。它还可以用于添加/读取请求元数据。
*   `method string` -被调用的 RPC 方法的名称。
*   `req interface{}` -请求实例，这是一个`interface{}`作为反射用于编组
*   `reply interface{}` -响应实例，工作方式与`req`参数相同
*   `cc *grpc.ClientConn` -底层客户端到服务器的连接。
*   `invoker grpc.UnaryInvoker`-RPC 调用方法。类似于您调用`ServeHTTP`的 [HTTP 中间件](https://gist.github.com/gbbr/935f26e50080ae99eedc822d8c273a89#file-middleware_funcs-go)，这需要被调用以进行 RPC 调用。
*   `opts ...grpc.CallOption` -用于配置 gRPC 调用的`grpc.CallOption`实例。

有了所有这些，我们就获得了关于正在进行的呼叫的许多信息。这使得创建类似于[日志中间件](https://github.com/grpc-ecosystem/go-grpc-middleware/blob/master/logging/logrus/client_interceptors.go)的东西变得非常简单，它将写出 RPC 调用信息。

#### 一元服务器拦截器

服务器拦截器看起来与客户机非常相似，只是它们允许我们修改从 gRPC 调用返回的响应。这是函数签名，它被定义为`grpc.UnaryServerInterceptor` :

```
func Interceptor(ctx context.Context, req interface{}, info *grpc.UnaryServerInfo, handler grpc.UnaryHandler) (interface{}, error) {
    // Invoke 'handler' to use your gRPC server implementation and get
    // the response.
} 
```

Enter fullscreen mode Exit fullscreen mode

与客户端一样，这里有几个不同的参数:

*   `ctx context.Context` -这是请求上下文，主要用于超时。它还可以用于添加/读取请求元数据。
*   `req interface{}` -入站请求
*   `info *grpc.UnaryServerInfo` -处理请求的 gRPC 服务器的信息
*   `handler grpc.UnaryHandler` -入站请求的处理程序，您需要调用它，否则您将无法得到对客户端的响应。

#### 流客户端拦截器

使用 streams 的工作非常相似，下面是`grpc.StreamClientInterceptor` :
的签名

```
func Interceptor(ctx context.Context, desc *grpc.StreamDesc, cc *grpc.ClientConn, method string, streamer grpc.Streamer, opts ...grpc.CallOption) (grpc.ClientStream, error) {
    // Call 'streamer' to write messages to the stream before this function returns
} 
```

Enter fullscreen mode Exit fullscreen mode

*   `ctx context.Context` -这是请求上下文，主要用于超时。它还可以用于添加/读取请求元数据。
*   `desc *grpc.StreamDesc` -表示流式 RPC 服务的方法规范。
*   `cc *grpc.ClientConn` -底层客户端到服务器的连接。
*   `method string` -被调用的 gRPC 方法的名称。
*   被拦截器调用来创建一个流。

#### 流服务器拦截器

下面是`grpc.StreamServerInterceptor`的签名

```
func Interceptor(srv interface{}, stream grpc.ServerStream, info *grpc.StreamServerInfo, handler grpc.StreamHandler) error {
    // Call 'handler' to invoke the stream handler before this function returns
} 
```

Enter fullscreen mode Exit fullscreen mode

*   `srv interface{}` -服务器实现
*   `stream grpc.ServerStream` -定义流式 RPC 的服务器端行为。
*   `info *grpc.StreamServerInfo` -关于服务器端的流式 RPC 的各种信息
*   `handler grpc.StreamHandler`-gRPC 服务器调用的处理程序，用于完成流式 RPC 的执行

### 创建拦截器

在这篇文章中，假设我们有一个 gRPC 客户机和服务器，它们通过一个 HTTP API 获得的 JWT 令牌进行身份验证。如果提供的 JWT 令牌不再有效，服务器将返回一个适当的状态代码，拦截器将检测到该代码，触发对 HTTP API 的调用来刷新令牌。我们将使用一元客户端拦截器来实现这一点，但是代码可以很容易地移植到客户端流和服务器。

**注意:**gRPC 上有很多基于令牌的认证的开源实现，本文中的代码只是作为一个例子。理想情况下，您需要比用户名和密码组合更强大的东西。您可以在[grpc-生态系统/go-grpc-中间件](https://github.com/grpc-ecosystem/go-grpc-middleware)库中查看许多不同的拦截器实现

首先，我们需要一个类型来存储我们的 JWT 令牌和认证细节，我们将使用基本认证来获取令牌。

```
type (
    JWTInterceptor struct {
        http *http.Client // The HTTP client for calling the token-serving API
        token string // The JWT token that will be used in every call to the server
        username string // The username for basic authentication
        password string // The password for basic authentication
        endpoint string // The HTTP endpoint to hit to obtain tokens
    }
) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要一元客户端拦截器，它会将 JWT 令牌添加到每个出站调用的请求元数据中，我们遵循的是[承载令牌](https://oauth.net/2/bearer-tokens/)方法:

```
func (jwt *JWTInterceptor) UnaryClientInterceptor(ctx context.Context, method string, req interface{}, reply interface{}, cc *grpc.ClientConn, invoker grpc.UnaryInvoker, opts ...grpc.CallOption) error {
    // Add the current bearer token to the metadata and call the RPC
    // command
    ctx = metadata.AppendToOutgoingContext(ctx, "authorization", "bearer "+t.token)
    return invoker(ctx, method, req, reply, cc, opts...)
} 
```

Enter fullscreen mode Exit fullscreen mode

只要 JWT 令牌有效，上述操作就会有效。如果令牌过期，我们最终将无法再调用服务器。所以我们需要一个方法来调用为我们提供令牌的 HTTP API。API 接受 JSON 主体并在响应主体中返回令牌，我们还需要一些类型来表示这些

```
type(
    authResponse struct {
        Token string `json:"token"`
    }

    authRequest struct {
        Username string `json:"username"`
        Password string `json:"password"`
    }
) 
```

Enter fullscreen mode Exit fullscreen mode

以下是获取新 JWT 代币的函数。被调用的 API 将返回一个 200 响应，带有一个包含令牌的 JSON 编码的主体。它使用`http.Error`返回错误，所以这些只是字符串响应。一旦我们有了令牌，我们就将它设置在`JWT`结构上以备后用。

```
func (jwt *JWTInterceptor) refreshBearerToken() error {
    resp, err := jwt.performAuthRequest()

    if err != nil {
        return err
    }

    var respBody authResponse
    if err = json.NewDecoder(resp.Body).Decode(&respBody); err != nil {
        return err
    }

    jwt.token = respBody.Token

    return resp.Body.Close()
}

func (jwt *JWTInterceptor) performAuthRequest() (*http.Response, error) {
    body := authRequest{
        Username: jwt.username,
        Password: jwt.password,
    }

    data, err := json.Marshal(body)

    if err != nil {
        return nil, err
    }

    buff := bytes.NewBuffer(data)
    resp, err := jwt.http.Post(jwt.endpoint, "application/json", buff)

    if err != nil {
        return resp, err
    }

    if resp.StatusCode != http.StatusOK {
        out := make([]byte, resp.ContentLength)
        if _, err = resp.Body.Read(out); err != nil {
            return resp, err
        }

        return resp, fmt.Errorf("unexpected authentication response: %s", string(out))
    }

    return resp, nil
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这些定义，我们可以像这样更新我们的拦截器逻辑:

```
func (jwt *JWTInterceptor) UnaryClientInterceptor(ctx context.Context, method string, req interface{}, reply interface{}, cc *grpc.ClientConn, invoker grpc.UnaryInvoker, opts ...grpc.CallOption) error {
    // Create a new context with the token and make the first request
    authCtx := metadata.AppendToOutgoingContext(ctx, "authorization", "bearer "+jwt.token)
    err := invoker(authCtx, method, req, reply, cc, opts...)

    // If we got an unauthenticated response from the gRPC service, refresh the token
    if status.Code(err) == codes.Unauthenticated {
        if err = jwt.refreshBearerToken(); err != nil {
            return err
        }

        // Create a new context with the new token. We don't want to reuse 'authCtx' here
        // because we've already appended the invalid token. We're appending metadata to
        // a slice here rather than a map like HTTP headers, so the first one will be picked
        // up and invalid.
        updatedAuthCtx := metadata.AppendToOutgoingContext(ctx, "authorization", "bearer "+jwt.token)
        err = invoker(updatedAuthCtx, method, req, reply, cc, opts...)
    }

    return err
} 
```

Enter fullscreen mode Exit fullscreen mode

### 测试拦截器

现在我们已经编写了拦截器，我们需要一些测试。当您的包没有定义使用的键时，在上下文中断言值可能有点棘手。幸运的是,`google.golang.org/grpc/metadata`包含了一些方法，我们可以用它们来获得我们需要的信息，并断言这就是我们所期望的。我们将实现我们自己版本的`invoker`方法，它将断言元数据中 JWT 令牌的存在。然后，我们可以在测试中直接调用`JWTInterceptor.UnaryClientInterceptor`方法，而不用连接或模仿 gRPC 服务。

我通常使用[表驱动测试](https://github.com/golang/go/wiki/TableDrivenTests)来编写，但是为了简洁起见，我将简单介绍一下从上下文中提取令牌并检查其值的步骤。

*   在您的自定义调用函数中，使用`metadata.FromOutgoingContext(ctx)`提取输出的元数据
*   使用上面的元数据，使用`metadata.NewIncomingContext(ctx, md)`将您的出站上下文转换为入站上下文。
*   使用`github.com/grpc-ecosystem/go-grpc-middleware/auth`和`AuthFromMD`方法提取 JWT 令牌。
*   如果令牌不是您所期望的或者是空的，使用`google.golang.org/grpc/codes`包返回`codes.Unauthenticated`。
*   使用 HTTP mock 捕获对令牌的请求并处理它。(要么使用标准库，要么使用类似于 [gock](https://github.com/h2non/gock) 的 HTTP 模仿包)

### 使用拦截器

编写好拦截器后，我们可以使用`grpc.With...`方法来应用它，比如:

```
// Create a new interceptor
jwt := &JWTInterceptor{
    // Set up all the members here
}

conn, err := grpc.Dial("localhost:5000", grpc.WithUnaryInterceptor(jwt.UnaryClientInterceptor))

// Perform the rest of your client setup 
```

Enter fullscreen mode Exit fullscreen mode

这同样适用于服务器。当你创建你的服务器时，你可以选择提供一元/流服务器拦截器。

### 链接

*   [https://grpc.io/](https://grpc.io/)
*   [https://grpc.io/docs/guides/concepts/#rpc-life-cycle](https://grpc.io/docs/guides/concepts/#rpc-life-cycle)
*   [https://github . com/grpc-ecosystem/go-grpc-middleware/blob/master/logging/logrus/client _ interceptors . go](https://github.com/grpc-ecosystem/go-grpc-middleware/blob/master/logging/logrus/client_interceptors.go)
*   [https://gist . github . com/gbbr/935 f 26 e 50080 AE 99 eedc 822 D8 c 273 a 89 # file-middleware _ funcs-go](https://gist.github.com/gbbr/935f26e50080ae99eedc822d8c273a89#file-middleware_funcs-go)
*   [https://oauth.net/2/bearer-tokens/](https://oauth.net/2/bearer-tokens/)
*   [https://github.com/grpc-ecosystem/go-grpc-middleware](https://github.com/grpc-ecosystem/go-grpc-middleware)
*   [https://github . com/golang/go/wiki/table driventest](https://github.com/golang/go/wiki/TableDrivenTests)
*   [https://github.com/h2non/gock](https://github.com/h2non/gock)
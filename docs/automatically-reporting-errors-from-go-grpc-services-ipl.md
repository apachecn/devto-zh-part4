# 从 Go gRPC 服务自动报告错误

> 原文：<https://dev.to/aknudsen/automatically-reporting-errors-from-go-grpc-services-ipl>

## 简介

在开发(微)服务时，我总是看到需要实现内部错误的自动报告，无论是语言还是框架。我已经用 Node.js HTTP 和 gRPC 服务做了很多年了，现在我正在用 Go 编写 gRPC 服务，我必须学习如何在这个技术栈中完成它。因此，我写了这篇教程来展示它是如何做到的。

由于 Go 中有两种基本类型的错误，即`error`实例和 panic，为了检测用 Go 编写的 gRPC 服务中的内部错误，您必须捕获从请求处理程序返回的非客户端错误和陷阱 panic。通过做一些研究，我发现实现这一点的最佳方式是使用 Go-gRPC 所谓的[拦截器](https://github.com/grpc/grpc-go/tree/master/examples/features/interceptor)。这些本质上是钩子，让您处理传入的请求并自己调用处理程序。

当谈到实际的错误报告时，我在本教程中选择使用电子邮件，更具体地说是使用 [Mandrill](https://mandrillapp.com) 服务来发送它们。我选择 Mandrill 没有特别的原因，除了我自己已经使用这个服务很多年了，并且可以很容易地使用它。如果您愿意，您可能希望调整代码以使用另一种邮件服务(毕竟这是开源的好处)。

本教程将讨论我写的 gRPC-Go 微服务的一个[例子，它除了分别提供内部错误失败和死机的方法之外什么也不做。如果您正确配置了该服务，它将向您提供的电子邮件地址报告这两种类型的内部错误。](https://gitlab.com/aknudsen/error-reporting-go-grpc-service)

## 添加 gRPC 拦截器

因为请求处理程序是非流式的，所以我使用
[`UnaryInterceptor`](https://godoc.org/google.golang.org/grpc#UnaryInterceptor) 选项来处理`grpc.NewServer`，并用[拦截器函数](https://gitlab.com/aknudsen/error-reporting-go-grpc-service/blob/master/server/interceptor.go#L83)实例化它。拦截器做两件事:检测内部错误并报告它们，捕获并报告混乱。我将在接下来的两节中讨论这两种场景。拦截器的代码如下:

```
// Interceptor intercepts every gRPC request.
// If an error with either the code Unknown or Internal is returned from the handler,
// we report it by email.
func NewInterceptor(conf Config) grpc.UnaryServerInterceptor {
  return func(ctx context.Context, req interface{}, info *grpc.UnaryServerInfo,
    handler grpc.UnaryHandler) (resp interface{}, err error) {
    logger := log.With().Str("component", "server.interceptor").Logger()
    logger.Debug().Msgf("Processing request")

    defer func() {
      if r := recover(); r != nil {
        err = status.Errorf(codes.Internal, "panic: %s", r)
        logger.Error().Msgf("Panic detected: %s", r)
        if e := SendBugMail(errors.Wrap(err, 0), conf, logger); e != nil {
          logger.Error().Err(e).Msgf("Reporting bug by email failed")
        }
      }
    }()

    resp, err = handler(ctx, req)
    errCode := status.Code(err)
    if errCode == codes.Unknown || errCode == codes.Internal {
      logger.Debug().Err(err).Msgf("Request handler returned an internal error - reporting it")
      if e := SendBugMail(errors.Wrap(err, 0), conf, logger); e != nil {
        logger.Error().Err(e).Msgf("Reporting bug by email failed")
      }

      return
    }

    logger.Debug().Msgf("Request finished successfully")
    return
  }
} 
```

## 报告请求处理程序的内部错误

检测内部错误(即不是由 gRPC 客户端引起的错误)非常简单。只是检查请求处理程序返回的错误是有代码`Unknown`还是`Internal`的问题。如果返回的错误包含这些代码中的任何一个，就会通过电子邮件向配置的收件人发送错误报告。我在下面包含了负责检测和处理来自请求处理程序的内部错误的源代码片段:

```
resp, err = handler(ctx, req)
errCode := status.Code(err)
if errCode == codes.Unknown || errCode == codes.Internal {
  logger.Debug().Err(err).Msgf("Request handler returned an internal error - reporting it")
  if e := SendBugMail(errors.Wrap(err, 0), conf, logger); e != nil {
    logger.Error().Err(e).Msgf("Reporting bug by email failed")
  }

  return
}

logger.Debug().Msgf("Request finished successfully")
return 
```

值得一提的是，我使用了一个特殊的错误包装库，
[go-errors/errors](https://github.com/go-errors/errors) ，来获得错误堆栈跟踪，因为这些对于普通的 go 错误是不可用的。错误报告包括格式化的 stacktrace，这对追踪错误非常有帮助。

## 请求处理程序中的报告混乱

为了捕获请求处理程序中的混乱，必须使用
[恢复](https://blog.golang.org/defer-panic-and-recover)功能。更重要的是，它必须在拦截器中被调用，因为每个请求都在它自己的 goroutine 中被处理，如果在主 goroutine 中调用了`recover`,它就不会在请求处理程序中陷入混乱。

正如你从[中看到的，代码](https://gitlab.com/aknudsen/error-reporting-go-grpc-service/blob/master/server/interceptor.go#L88)，`recover`在一个延迟函数中被调用。这对`recover`捕获恐慌是必要的，它也使我们能够修改从拦截器返回的错误(通过重新分配给指定的返回变量`err`)。我选择返回错误代码`Internal`，而不是像默认行为那样中止进程。在 gRPC 中间件中，这样做显然是常见的做法，而且由于请求处理程序出现问题而导致进程崩溃似乎没有什么好处，所以我也这样做了。

```
defer func() {
  if r := recover(); r != nil {
    err = status.Errorf(codes.Internal, "panic: %s", r)
    logger.Error().Msgf("Panic detected: %s", r)
    if e := SendBugMail(errors.Wrap(err, 0), conf, logger); e != nil {
      logger.Error().Err(e).Msgf("Reporting bug by email failed")
    }
  }
}() 
```

## 发送 Bug 报告

让我们讨论一下错误报告的发送。如前所述，我决定在本教程中使用 Mandrill 邮件服务，原因很简单，因为这是我最熟悉的服务。错误报告的发送封装在函数 [`SendBugMail`](https://gitlab.com/aknudsen/error-reporting-go-grpc-service/blob/master/server/interceptor.go#L21) 中。它使用了 [Mandrill REST API](https://mandrillapp.com/api/docs/) ，非常简单地编写了一个包含错误报告的 HTML 文档，包括错误堆栈跟踪，并通过所需的参数将其作为 JSON 对象发布到 Mandrill 端点。

```
func SendBugMail(caughtErr *errors.Error, conf Config, logger zerolog.Logger) error {
  client := &http.Client{
    Timeout: time.Minute,
  }

  msg := strings.ReplaceAll(caughtErr.ErrorStack(), "\n", "<br>")
  version := "(master)"
  dateTimeStr := time.Now().UTC().String()
  body := map[string]interface{}{
    "key": conf.MandrillSecret,
    "message": map[string]interface{}{
      "html": fmt.Sprintf(`<p>%s - an error was detected in Service
%s.</p>

<pre><code>%s</code></pre>
`, dateTimeStr, version, msg),
      "subject":    fmt.Sprintf("Error Detected in Service %s", version),
      "from_email": conf.SenderEmail,
      "from_name":  conf.SenderName,
      "to": []map[string]string{
        map[string]string{
          "email": conf.RecipientEmail,
          "name":  conf.RecipientName,
          "type":  "to",
        },
      },
      "headers": map[string]string{
        "Reply-To": conf.SenderEmail,
      },
    },
  }

  var bodyJson []byte
  bodyJson, err := json.Marshal(body)
  if err != nil {
    return err
  }
  resp, err := client.Post("https://mandrillapp.com/api/1.0/messages/send.json", "application/json",
    bytes.NewReader(bodyJson))
  if err != nil {
    return err
  }
  defer resp.Body.Close()

  b, err := ioutil.ReadAll(resp.Body)
  if err != nil {
    return err
  }
  var respBody map[string]interface{}
  if err := json.Unmarshal(b, &respBody); err == nil && respBody["status"] == "error" {
    return errors.Errorf("Mailing failed: %s", respBody["message"])
  }

  logger.Debug().Msgf("Finished sending bug report by mail")

  return nil
} 
```

## 测试

为了测试服务和它的错误报告，你首先需要一个 [Mandrill](https://mandrillapp.com/) 账户。然后为该帐户获取一个 API 令牌，并在服务 Git 存储库中创建一个 config.toml 文件，内容如下(确保替换尖括号中的值):

```
mandrillSecret = <mandrill-token>
senderEmail = <email-address-for-sender>
senderName = <name-of-sender>
recipientEmail = <email-address-for-recipient>
recipientName = <name-of-recipient>. 
```

创建 config.toml 后，在终端进入仓库，启动服务器:

```
$ ./build/service server
1:40PM INF listening on port 9000... component=app 
```

这样做之后，您可以在另一个终端中测试错误和紧急检测:

```
$ ./build/service fail
1:40PM INF Calling service
1:40PM INF Called service successfully
$ ./build/service panic
1:41PM INF Calling service
1:41PM INF Called service successfully 
```

如果您已经正确地配置了所有的东西，您应该会收到一些错误报告。

## 结论

就是这样！希望这篇教程对你学习如何在 Go 中最好地编写 gRPC 微服务有用！
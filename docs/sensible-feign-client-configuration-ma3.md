# 合理的虚拟客户端配置

> 原文：<https://dev.to/philhardwick/sensible-feign-client-configuration-ma3>

Feign clients 使得基于已知的 Spring 注释编写 restful 客户端变得容易。它还包括与其他网飞库的负载和良好的微服务模式(如服务发现、负载平衡和断路器)的集成。

Feign 是可配置的，但是在将它用于服务到服务调用之前，您通常会希望更改配置。

## 测井

您将希望看到发出了什么请求。这需要您做两件事:在 application.yml 中为您的 feign 配置设置 loggerLevel，并将 feign 客户端类的日志记录级别设置为 DEBUG。

```
logger.level:
  root: INFO 
  com.example.clients.InvoiceClient: DEBUG

feign:
  client:
    config:
      default:
        loggerLevel: basic 
```

如果您也想在日志中看到标题和响应正文，您可以将`loggerLevel`设置为`full`。

## 是否解码 404s

如果您希望在客户端收到 404 Not Found 响应时引发 FeignException，可以将 decode404 设置为 false，这是默认设置。否则，如果您将 decode404 设置为 true，您将收到 null 作为响应，或者如果您已经将 retrun 类型包装在 Optionals 中，您将收到 Optional。

## 错误解码器

如何处理错误是最重要的事情，因为如果你有的话，你不想给你的客户返回合理的回应，并且当事情出错时，让调查变得容易。我认为明智的做法是:

1.  出现任何服务器错误时重试(状态> 499)
2.  重试失败时返回相同的服务器错误
3.  在任何 429 或设置了重试后标题时重试
4.  当任何其他客户端错误发生时，返回 500
5.  记录错误的状态、导致错误的方法以及响应正文

这是一个相当多的需求列表。让我们建立一个满足这个条件的错误解码器:

```
public class CustomErrorDecoder implements ErrorDecoder {

    private static final Logger LOG = LoggerFactory.getLogger(CustomErrorDecoder.class);
    private ErrorDecoder defaultDecoder = new ErrorDecoder.Default();

    @Override
    public Exception decode(String methodKey, Response response) {
        //Requirement 5: log error first and include response body
        try {
            LOG.error("Got {} response from {}, response body: {}", response.status(), methodKey, IOUtils.toString(response.body().asReader()));
        } catch (IOException e) {
            LOG.error("Got {} response from {}, response body could not be read", response.status(), methodKey);
        }
        Exception defaultException = defaultDecoder.decode(methodKey, response);
        if (defaultException instanceof RetryableException.class) {
            //Requirement 3: retry when Retry-After header is set
            //Will be true if Retry-After header is set e.g. in case of 429 status
            return defaultException;
        }
        if (HttpStatus.valueOf(response.status()).is5xxServerError()) {
            //Requirement 1: retry on server error
            return new RetryableException("Server error", response.request().httpMethod(), new ServerErrorResponseFromOtherSystemException(HttpStatus.valueOf(response.status()), defaultException), null);
        } else {
            //Requirement 4: return 500 on client error
            return new ClientErrorResponseFromOtherSystemException("Client error " + response.status() + " from calling other system", defaultException);
        }
    }

} 
```

而投掷中的例外:

```
//Requirement 4: return 500 on client error
@ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
public class ClientErrorResponseFromOtherSystemException extends Exception {

    public ThirdPartyClientErrorResponseException(String message, Exception exception) {
        super(message, exception);
    }
}

public class ServerErrorResponseFromOtherSystemException extends Exception {
    private HttpStatus responseStatusFromOtherSystem;

    public ServerErrorResponseFromOtherSystemException(HttpStatus httpStatus, Exception exception) {
        this.responseStatusFromOtherSystem = httpStatus;
        super(message, exception);
    }

    public HttpStatus getStatus() {
        return responseStatusFromOtherSystem;
    }
} 
```

`@ResponseStatus`注释意味着当注释的异常到达控制器时，spring 将返回该状态。为了确定`ServerErrorResponseFromOtherSystemException`的响应状态，我们需要一个控制器的异常处理器:

```
@RestController
public class InvoiceController {

    //Handler methods

    @ExceptionHandler(ServerErrorResponseFromOtherSystemException.class)
    public void ResponseEntity handleServerErrorResponseException(ServerErrorResponseFromOtherSystemException ex) {
        //Requirement 2: return the same error when retry is exhausted
        return ResponseEntity.status(exception.getStatus()).build();
    }

} 
```

对于 feign 来说，需要将 retryer 暴露为 bean。为了能够将异常传播策略设置为 unwrap，我们还需要将一个带有我们定制的 feign builder 作为 bean 来公开。参见下面的例子:

```
@Bean
public Retryer retryer() {
    return new Retryer.Default();
}

@Bean
public Feign.Builder feignBuilder(Retryer retryer) {
    return Feign.builder()
            .exceptionPropagationPolicy(ExceptionPropagationPolicy.UNWRAP)
            .errorDecoder(new ServerErrorRetryingErrorDecoder())
            .retryer(retryer);
} 
```

将异常传播策略设置为 unwrap 意味着`RetryableException`中的异常是重试次数用尽时抛出的异常，允许抛出特定于应用程序的异常，然后由异常处理程序处理(同样，假设您使用的是 Spring MVC)。

优雅地处理错误是必要的，从一开始就构建它是一个好的实践——不要等到客户注意到第三方系统正在失败。我发现即使是最可靠的第三方系统也会偶尔超时并返回 502。我在生产中遇到过这种情况，发生了一个坏的网关错误，这是我在其他环境中从未见过的。幸运的是，我实现了这个重试机制，请求被重试，客户在短暂等待后继续旅行。
# 重试模式:示例和建议

> 原文：<https://dev.to/apium_hub/retry-pattern-examples-recommendations-459p>

今天我想谈谈**重试模式**。在服务和/或外部资源之间进行持续通信的分布式应用程序中，当与这些环境交互时，可能会出现临时或暂时的故障(暂时故障)。这些故障可能是由不同的原因造成的，其中最常见的是网络连接的瞬间丢失、暂时不可用的服务、超过响应时间等。

通常情况下，这些错误会在很短的时间内自动解决，因此如果调用服务或资源，它会立即正确响应。暂时性错误的一个典型例子是，由于同时连接的峰值超过了每个配置允许的最大数量，导致无法连接到数据库。

[![](img/cbb7f143a4890802f4425b32d6b470ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y1Yydlq_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apiumhub.com/wp-content/uploads/2019/06/Screenshot-2019-06-30-at-21.02.30.png)

然而，尽管这些错误很少发生，但应用程序必须正确处理这些错误，以尽量减少对它的影响。这个问题的一个可能的解决方案是应用**重试模式**。

## 重试模式

**重试模式**被称为稳定模式，顾名思义，它是关于重试失败的操作。实际上，这是一个非常简单的定义，需要补充的是，根据检测到的错误类型和/或尝试次数，可以执行各种操作:

*   *重试:*如果错误表明是临时故障或非典型故障，应用程序可以立即重试相同的操作，因为相同的错误可能不会再次发生。
*   *等待一段时间后重试:*如果错误是由于网络连接问题或服务请求高峰而发生的，在尝试再次执行操作之前，最好等待一段时间。
*   *取消:*如果错误表明我们没有面临暂时的失败，则应该取消操作，并正确报告或管理错误。

这些操作可以结合起来创建一个重试策略，以适应我们的应用程序的需要。

[![](img/942b144729b0c81c726290e78820e05c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VjjcnA3E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apiumhub.com/wp-content/uploads/2019/06/Retry_pattern_flow.png)

这是 Kotlin 中一个简单实现的例子，其中只考虑失败的尝试次数，并且在每次重试之间有一个等待时间:

```
 fun run(action: () -> T): T {
        return try {
            action.invoke()
        } catch (e: Exception) {
            lastFailure = LocalDateTime.now()
            retry(action)
        }

    }

    @Throws(RuntimeException::class)
    private fun retry(action: () -> T): T {
        retryCounter = 1
        while (retryCounter < maxRetries) {
            if (isTimerExpired) {
                try {
                    return action.invoke()
                } catch (ex: Exception) {
                    retryCounter++
                    if (retryCounter >= maxRetries) {
                        break
                    }

                }
            }
        }

        throw RuntimeException("Command fails on all retries")
    } 
```

当然，实现可以复杂得多。例如，我们可以有一个从连续重试策略开始的实现，因为服务很快恢复是正常的。如果在多次连续重试后，错误仍然存在，我们可以继续在重试之间加入一个谨慎的等待期，最后，如果服务仍然没有恢复，我们可以继续取消操作。

[![Global Software Architecture Summit](img/c1985e5051debce02eafb384ce2cd833.png)](https://gsas.io/) 实现的复杂性必须响应我们应用的实际需求和业务需求。

有一些库以简单的方式实现了重试模式，比如 Spring Retry。

```
 @Configuration
    @EnableRetry
    class Application {

        @Bean
        fun service(): Service {
            return Service()
        }

    }

   @Service
   class Service {

        @Retryable(maxAttempts = 2, include = [RemoteAccessException::class])
        fun service() {
            // ... do something
            logger.info("Success calling external service")
        }

        @Recover
        fun recover(e: RemoteAccessException) {
            // ... do something when call to service fails
            logger.error("Recover for external service")
        }
    } 
```

正如我们所看到的，通过这个库实现非常简单。在示例中，我们可以看到要做的第一件事是用注释@EnableRetry 配置应用程序。

接下来，我们添加@Retryable 注释，在出现错误的情况下，使用该注释来指示将要“重试”的方法。当错误属于 RemoteAccessException 类型时，如果超过最大尝试次数(maxAttempts = 2 ),则@Recover 批注指示执行将在何处继续。

你可以在 GitHub 上找到完整的例子(链接在文章末尾)。

我们可以在控制台输出中检查模式的流程。执行是成功的，直到它发现一个暂时性错误，此时它已经按照配置中的指定重新尝试了操作两次，并且当相同的错误继续时，它已经通过恢复方法退出。

```
INFO 81668 --- [Logge : RetryService] - Calling external service...
INFO 81668 --- [Logge : RetryService] - Success calling external service
INFO 81668 --- [Logge : RetryService] - Calling external service...
INFO 81668 --- [Logge : RetryService] - Success calling external service
INFO 81668 --- [Logge : RetryService] - Calling external service...
INFO 81668 --- [Logge : RetryService] - Calling external service...
ERROR 81668 --- [Logger : RetryService] - Recover output 
```

当错误是暂时的、零星的，并且在稍后的调用中得到解决时，这种模式非常有效，但是在应用它时必须考虑一系列的因素:

*   *错误的类型:*一定是告诉我们可以快速恢复的错误。
*   *错误的严重程度:*重试操作会对应用程序的性能产生负面影响。在某些情况下，更好的方法是管理错误，并让用户决定是否要重试该操作。
*   *重试策略:*连续重试操作的策略，尤其是没有等待时间的策略，可能会恶化远程服务的状态。
*   *附带影响:*如果运算不是幂等的，就不能保证重试会得到预期的结果。

在以下情况下，不建议使用这种模式:

*   管理与连接或服务故障无关的非暂时性错误(如业务逻辑错误)。
*   长期存在的错误。等待时间和必要的资源太多。对于这些情况，有一些解决方案，比如断路器模式的应用，我们将在另一篇文章中讨论。

我想强调的是，强烈建议保留失败操作的记录，因为这是非常有用的信息，有助于正确确定项目基础设施的规模，并通过应用程序的错误管理找到重复出现的错误和隐藏的错误。

您可以在我们的 [GitHub](https://github.com/ldavidgomez/retry-pattern) 中找到本文中讨论的完整示例。

帖子[重试模式:示例&推荐](https://apiumhub.com/tech-blog-barcelona/retry-pattern-examples-recommendations/)最早出现在 [Apiumhub](https://apiumhub.com) 上。
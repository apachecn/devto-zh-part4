# Java 微服务的弹性模式。断路器。

> 原文：<https://dev.to/silviobuss/resilience-pattern-for-java-microservices-the-circuit-breaker-b2g>

## 简介

尽管微服务架构的优势是众所周知的(这里不解释这个话题)，但我们经常忽略系统设计中的弹性。软件系统对运行在不同进程中的软件进行远程调用，通常是在网络上的不同机器上。例如:

[![Example](img/221c607dcc0aba15bf15d8a38baa6d01.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r5uhwqep--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vc7jkkxycp5ldnt9lp0d.PNG)

当**服务 B** 不可用、高延迟响应或重复返回相同的业务异常时，会发生什么情况？这些未处理的情况可能导致连锁故障，从而影响公司的各种服务。

## 断路器设计

断路器背后的基本思想非常简单。您将一个受保护的函数调用包装在一个断路器对象中，该对象监视它的故障。当我们应用这个模式时，我们防止了可能的应用程序问题。该模式遵循与名为断路器的安全电气组件相同的概念。

一旦故障达到某个阈值，断路器就会跳闸，对断路器的所有进一步调用都会返回一个错误或一些替代服务或默认消息，而根本不会进行受保护的调用。这将确保系统有响应，并且线程不会等待无响应的调用，从而保护系统避免灾难性故障。

如果**服务 B** 出现故障，**服务 A** 仍应尝试从故障中恢复，并尝试执行以下操作之一:

**自定义回退**:尝试从其他来源获取相同的数据。如果不可能，请使用它自己的缓存值或您的自定义客户端错误响应。

**Fail fast** :如果服务 A 知道服务 B 停机，等待超时并消耗自己的资源是没有意义的。

**自动修复**:定期检查服务 B 是否再次工作。

**其他 API 应该工作**:所有其他 API 应该继续工作。

## 它是如何工作的？

[![Circuit working](img/d7ca7e56c67bf062f1e9e58c1a01817f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LDr4sIMX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bk0c85is3c5767cc3qzt.png)

**闭合:**一切正常时，断路器保持闭合，所有对服务 B 的调用正常发生。如果失败的次数超过预定的限制，则状态变为打开。

**打开:**在这种状态下，断路器不会执行服务 B 调用，并返回一个已处理的错误。

**半开:**超时后，电路切换到半开状态，测试潜在问题是否仍然存在。如果在这种半开状态下单个呼叫失败，断路器将再次跳闸。如果成功，重置回正常的关闭状态。

## 结论

断路器有助于防止微服务之间可能出现的集成问题。为了获得最佳结果，请使用监控工具和指标，如 prometheus 和 grafana。

在下一篇文章中，我将谈论 Java 应用弹性的主要框架 **Resilience4j** 。

[![silviobuss](img/f8087747d0b6d8dcae125b2c80a852dc.png)](/silviobuss) [## Java 微服务的弹性。带弹性 4j 的断路器

### Silvio Buss ・ Jun 14 '19 ・ 2 min read

#java #microservices #opensource #devops](/silviobuss/resilience-for-java-microservices-circuit-breaker-with-resilience4j-5c81)

### 参考文献

[https://martinfowler.com/bliki/CircuitBreaker.html](https://martinfowler.com/bliki/CircuitBreaker.html)
[https://dzone.com/articles/circuit-breaker-pattern](https://dzone.com/articles/circuit-breaker-pattern)
[https://it next . io/understand-circuit breaker-design-pattern-with-simple-practical-example-92a 752615 b42](https://itnext.io/understand-circuitbreaker-design-pattern-with-simple-practical-example-92a752615b42)
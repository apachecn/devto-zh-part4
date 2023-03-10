# Spring Boot

> 原文：<https://dev.to/vikashagrawal/spring-boot-4ehk>

# Spring 框架提供了哪些好处:

依赖注入:提供一个松散耦合的应用程序
复制/管道代码
非常关键

```
o   Spring JDBC
o   Spring MVC
o   Spring AOP
o   Spring ORM
o   Spring JMS
o   Spring Test 
```

Enter fullscreen mode Exit fullscreen mode

# 弹簧靴支柱项目:

自动配置:当我们使用 Spring MVC 时，我们需要配置组件扫描、dispatcher servlet、视图解析器、web jars(用于交付静态内容)等等。
Spring Boot 看着

```
o   Frameworks available on the CLASSPATH
o   Existing configuration for the application. 
```

Enter fullscreen mode Exit fullscreen mode

基于这些，Spring Boot 提供了用这些框架配置应用程序所需的基本配置。
所有的自动配置逻辑都在 spring-boot-autoconfigure.jar 中实现
一旦我们在项目中添加 Spring Boot 启动 Web 作为依赖项，Spring Boot 自动配置就会发现 Spring MVC 在类路径中。它自动配置 dispatcherServlet、默认错误页面和 webjars。
·Spring Boot 启动项目
所有的 web 应用程序都需要 Spring MVC、Jackson Databind(用于数据绑定)、Hibernate-Validator(用于使用 Java 验证 API 的服务器端验证)和 Log4j(用于日志记录)以及所有这些框架的兼容版本。

```
o   spring-boot-starter-web-services - SOAP Web Services
o   spring-boot-starter-web - Web & RESTful applications
o   spring-boot-starter-test - Unit testing and Integration Testing
o   spring-boot-starter-jdbc - Traditional JDBC
o   spring-boot-starter-hateoas - Add HATEOAS features to your services
o   spring-boot-starter-security - 
 Authentication and Authorization using Spring Security.
 Basic Authentication is the default.
 Using default security password, Default userid is user. Default password is printed in the server start up log.
o   spring-boot-starter-data-jpa - Spring Data JPA with Hibernate
o   spring-boot-starter-cache - Enabling Spring Framework’s caching support
o   spring-boot-starter-data-rest - Expose Simple REST Services using Spring Data REST
o   spring-boot-starter-actuator - To use advanced features like monitoring & tracing to your application out of the box
o   spring-boot-starter-undertow, spring-boot-starter-jetty, spring-boot-starter-tomcat - To pick your specific choice of Embedded Servlet Container
o   spring-boot-starter-logging - For Logging using logback
o   spring-boot-starter-log4j2 - Logging using Log4j2 
```

Enter fullscreen mode Exit fullscreen mode

Spring Boot 起动机母公司

```
o   All Spring Boot projects typically use spring-boot-starter-parent as the parent in pom.xml.
o   Spring Boot Starter Parent defines spring-boot-dependencies as the parent pom. It inherits dependency management from spring-boot-dependencies. Details in the next section.
o   Parent Poms allow you to manage the following things for multiple child projects and modules:
 Configuration - Java Version and Other Properties
 Dependency Management - Version of dependencies
 Default Plugin Configuration
o   A few other settings related to encoding and source, target version are also set in the parent pom.
<java.version>1.6</java.version><resource.delimiter>@</resource.delimiter> <!-- delimiter that doesn't clash with Spring ${} placeholders --><project.build.sourceEncoding>UTF-8</project.build.sourceEncoding><project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding><maven.compiler.source>${java.version}</maven.compiler.source><maven.compiler.target>${java.version}</maven.compiler.target>
o   Spring Boot Starter Parent inherit from spring-boot-dependencies, it shares all these characteristics as well.
<properties><activemq.version>5.13.4</activemq.version>...<ehcache.version>2.10.2.2.21</ehcache.version><ehcache3.version>3.1.1</ehcache3.version>...<h2.version>1.4.192</h2.version><hamcrest.version>1.3</hamcrest.version><hazelcast.version>3.6.4</hazelcast.version><hibernate.version>5.0.9.Final</hibernate.version><hibernate-validator.version>5.2.4.Final</hibernate-validator.version><hikaricp.version>2.4.7</hikaricp.version><hikaricp-java6.version>2.3.13</hikaricp-java6.version><hornetq.version>2.4.7.Final</hornetq.version><hsqldb.version>2.3.3</hsqldb.version><htmlunit.version>2.21</htmlunit.version><httpasyncclient.version>4.1.2</httpasyncclient.version><httpclient.version>4.5.2</httpclient.version><httpcore.version>4.4.5</httpcore.version><infinispan.version>8.2.2.Final</infinispan.version><jackson.version>2.8.1</jackson.version>....<jersey.version>2.23.1</jersey.version><jest.version>2.0.3</jest.version><jetty.version>9.3.11.v20160721</jetty.version><jetty-jsp.version>2.2.0.v201112011158</jetty-jsp.version><spring-security.version>4.1.1.RELEASE</spring-security.version><tomcat.version>8.5.4</tomcat.version><undertow.version>1.3.23.Final</undertow.version><velocity.version>1.7</velocity.version><velocity-tools.version>2.0</velocity-tools.version><webjars-hal-browser.version>9f96c74</webjars-hal-browser.version><webjars-locator.version>0.32</webjars-locator.version><wsdl4j.version>1.6.3</wsdl4j.version><xml-apis.version>1.4.01</xml-apis.version></properties> 
```

Enter fullscreen mode Exit fullscreen mode

# 什么是休息？

REST 代表代表性状态转移。REST 指定了一组架构约束。任何满足这些约束的服务都被称为 RESTful 服务。RESTful Web 服务的五个重要约束是

```
• Client - Server: There should be a service producer and a service consumer.
• The interface (URL) is uniform and exposing resources.
• The service is stateless.
• The service results should be Cacheable. HTTP cache, for example.
• Service should assume a Layered architecture. Client should not assume direct connection to server - it might be getting info from a middle layer - cache. 
```

Enter fullscreen mode Exit fullscreen mode

# 容错

当我们构建微服务架构时，有大量的小型微服务，它们都需要相互通信。
假设微服务 5 在某个时间点停机。这个问题的解决方案是在微服务出现故障的情况下有一个后备。微服务的这一方面称为容错。用于实现容错的一个流行框架是 Hystrix，它是网飞的一个开源框架。下面是一个相同的代码示例:

```
@GetMapping("/fault-tolerance-example")
@HystrixCommand(fallbackMethod="fallbackRetrieveConfguration")
public LimitConfiguration retrieveConfiguration() {
        throw new RuntimeException("Not Available");
}
public LimitConfiguration fallbackRetrieveConfiguration() {
        return new LimitConfiguration(999, 9);
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，如果 retrieveConfiguration()失败，则调用 fallbackRetrieveConfiguration，它返回硬编码的 LimitConfiguration 实例:
捕获异常和 Hystrix 之间的差异
现成提供的度量和仪表板可以帮助您查看您的系统和相关连接
通过使用不同的线程池实现隔板
更低的维护成本
健康检查能力。它提供了一个健康检查类，该类使用健康监控 API
作为插件。主要区别在于，Hystrix 在检测到错误时会打开电路(类似于电路),直到过了一段时间才会调用下游服务。这种行为可以防止级联中出现大量错误。它类似于智能红绿灯，因为知道你稍微晚一点就要出事，就变红灯不让你通过。经过一段可配置的时间后，电路再次闭合。您可以在 Hystrix 仪表板上看到“电路断开/闭合”:

# 负载均衡算法

Ribbon 提供了多种客户端负载平衡算法可供选择。

# 命名服务器

Eureka 命名服务器提供命名服务器来注册所有的微服务，它可以用来获取已注册的微服务的地址。

# 自动缩放

您可以使用 Docker 将每个微服务容器化并创建一个映像。Kubernetes 有能力管理容器。Kubernetes 可以配置为根据负载自动扩展。Kubernetes 可以识别应用程序实例，监控它们的负载，并自动伸缩。

# 集中记录

使用日志流是实现集中式日志记录的一种方式。实现它的常见方法是将微服务日志流式传输到一个公共队列。分布式日志记录服务器侦听队列并充当日志存储。它提供了搜索跟踪的功能。
流行的实现
一些流行的实现包括
用于集中日志记录的 ELK 堆栈(Elastic Search、Logstash 和 Kibana)
用于分布式跟踪的 Zipkin、Open Tracing API 和 Zaeger

# API 网关

在微服务中，我们通过 API 网关路由所有请求——包括内部和外部请求。我们可以在 API 网关中实现所有常见的特性，如认证、日志、审计和速率限制。
例如，您可能不希望微服务 3 被特定客户端调用超过 10 次。您可以将此作为 API 网关中速率限制的一部分。
您可以在 API gateway 中实现跨微服务的通用特性。一个流行的 API 网关实现是 Zuul API 网关。

# 集中配置

所有微服务(所有环境)的配置都存储在一个地方——集中式配置存储。当微服务需要其配置时，它会在启动时提供一个 id——微服务名称和环境的组合。
Spring 云配置服务器是云配置服务器的流行实现之一
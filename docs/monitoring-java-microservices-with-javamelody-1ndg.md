# 用 JavaMelody 监控 Java 微服务

> 原文：<https://dev.to/sandrogiacom/monitoring-java-microservices-with-javamelody-1ndg>

[**Javamelody**](https://github.com/javamelody/javamelody) 是一个开源项目，帮助你从你的服务中获取指标。有了它，您可以在将代码投入生产之前发现可能的故障。这很容易包含在你的应用程序中，在本教程中，我们将向你展示如何做到这一点。

## 创建演示应用

让我们开始创建一个 spring boot 应用程序。访问[https://start.spring.io/](https://start.spring.io/)生成我们的演示应用程序。您可以在组字段中使用自己的包名。
[![](img/ed4151622cf7bcdc125049ebe9fa8cd9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aAg0GmEu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qxo94wo5cpbfc2w6re2k.png) 
并且只添加 Web 依赖
[![](img/2ca1007f41750d68e255a9a73ec5d2d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w_GCqKmy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/50ksvzxpmxy7599s9iru.png) 
然后，生成项目。

我们的项目将生成一个 zip 文件，可以导入到您的首选 IDE 中。
一个生成的项目看起来是这样的:
[![](img/08c02531475c8c5611c701859a631bc8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--26_96q9W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t3avf2knwbxpc80hifd6.png)

如果你喜欢使用这个演示代码，克隆 github 项目:
[https://github.com/sandrogiacom/javamelody-spring-demo](https://github.com/sandrogiacom/javamelody-spring-demo)

## 添加 JavaMelody 依赖项

现在，让我们添加 javamelody 的 spring boot 依赖项

```
<dependency>
    <groupId>net.bull.javamelody</groupId>
    <artifactId>javamelody-spring-boot-starter</artifactId>
    <version>1.77.0</version>
</dependency> 
```

## 运行 app

接下来，您可以运行您的 spring-boot 应用程序并打开[http://localhost:8080/monitoring](http://localhost:8080/monitoring)来浏览监控报告。

度量将开始被捕获，但是我们的应用程序中仍然没有代码。让我们创建一个 rest 端点。

## 创建一个休息控制器

添加一个新的类 HelloController，如下:

```
@RestController
@RequestMapping("/hello")
public class HelloController {
    @GetMapping
    public String sayHello() {
        return "Hello JavaMelody!";
    }
} 
```

重启你的应用，打开[http://localhost:8080/hello](http://localhost:8080/hello)
多次运行这个网址。之后，再次打开[http://localhost:8080/monitoring](http://localhost:8080/monitoring)。

## 结果

如您所见，开始收集指标:

[![](img/95e657ca2a69883087757cc58a890833.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ilgCnO8e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vlki732zcpexdhc61con.png)

[![](img/18b2c835e1da79dd631414e4feb8799b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kFfeKf2l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t3v3yqcq400rziphs772.png)

## 更多 JavaMelody 资源

JavaMelody 有很多很多的特性，比如:

*   插件:(詹金斯，JIRA，竹子，救生筏，户外，声纳，Grails)
*   生成 PDF 报告(通过邮件发送每周、每天或每月报告)
*   脚本和警报(Jenkins 和 Groovy)
*   集中式监控服务器
*   真实用户监控
*   数据库监控
*   向 AWS CloudWatch 发送指标，Graphite
*   自定义报告、样式、图标和其他资源

## 结论

JavaMelody 帮助您预测应用程序的问题。为获得最佳效果，使用一些压力工具，如 [JMeter](https://jmeter.apache.org/) 。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[sandrogiacom](https://github.com/sandrogiacom)/[Java melody-spring-demo](https://github.com/sandrogiacom/javamelody-spring-demo)

### 如何用 Javamelody 监控你的 spring boot 应用程序

<article class="markdown-body entry-content container-lg" itemprop="text">

# javamelody-spring-demo

如何用 Javamelody 监控你的 spring boot 应用程序

[https://dev . to/sandrogiacom/monitoring-Java-micro services-with-javamelody-1 ndg](https://dev.to/sandrogiacom/monitoring-java-microservices-with-javamelody-1ndg)

</article>

[View on GitHub](https://github.com/sandrogiacom/javamelody-spring-demo)

[![](img/ad9b9da1674b387c7b673d8f5867bfc0.png)](https://twitter.com/sandrogiacom)
# Alexa 技能监控:将拦截器添加到 lambda 以获取日志

> 原文：<https://dev.to/kini/alexa-skills-monitoring-anadiendo-interceptores-al-lambda-para-tener-logs-5edf>

我在专栏里没有提到的一件事是 Alexa 的技能创造是后场监控的主题。我们必须能够以某种方式了解我们的系统如何与用户交互。尤其是当我们有事情失败而我们没有被这些错误所掩盖的时候。

我会考虑在这个监控问题上的两步:一方面，建立请求和响应日志；此外，在上述基础上，我们将制定指标和警报。在这封信里，我要讲的是第一件事。

让我们看一下如何在 lambda 上创建日志，以及如何使用 AWS 工具查看日志。

## 向 lambda 添加日志

将日志添加到技能文件夹与将日志添加到我们在移动应用程序或 web 应用程序中可能拥有的任何后端服务没有什么不同。取决于技术。有一些书店或其他。Java/Kotlin 通常使用 Apache log 4 J2，而 AWS Lambda 可以如其[官方文件中所述整合到我们的背面。](https://docs.aws.amazon.com/lambda/latest/dg/java-logging.html)

但是，如果我们不想把毯子绑在头上，想要一些简单的东西，就像我的情况一样，那就没有必要再加上任何东西了。AWS 中已经有了[【cloudwatch logs】](https://aws.amazon.com/es/cloudwatch/)，通过使用 lambda，我们可以查看我们在正常控制台上写的所有内容。

这样，我们就不必添加从属关系或配置文件。以 Kotlin 为例，使用`println`，我们将在 cloudwatch console 中看到这些消息。我知道这种情况并不常见，而且我们通常集成日志库来处理诸如消息级别(信息、调试、警告、错误等)之类的问题，但我不会使用它。

### Usando 拦截 para la 请求/响应

Alexa SDK 允许您在构建技能时添加请求、响应和侦听程序。我们将使用这些拦截器来获取请求内容和响应内容。
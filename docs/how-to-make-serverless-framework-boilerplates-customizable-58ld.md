# 如何使无服务器框架样板可定制

> 原文：<https://dev.to/theburningmonk/how-to-make-serverless-framework-boilerplates-customizable-58ld>

无服务器框架允许你使用语法`${file:(fileName):propertyName}`引用外部 JSON、YML 和 JS 文件。但是，您不能用运行时参数定制这些外部配置文件。

一位读者问我:

> 我有一个在我的 serverless.yml 中重复多次的样板文件，如何重用该样板文件，但只覆盖特定的字段

这是一个有趣的问题，这里有一些潜在的解决方案。

***免责声明*** *:以下示例使用 HTTP 事件源的唯一目的是用一个简单的配置对象说明解决方案。*

### YML 锚点

最简单的解决方案是使用 YML 锚。您可以定义和应用锚点并覆盖特定字段。例如:
# 威尔科尔。请求:为 Web API 自动生成 Fetch 和 XMLXHR 请求

> 原文：<https://dev.to/philipcoder/willcore-requests-auto-generating-fetch-and-xmlxhr-requests-for-web-api-35k8>

# [T1。NET CORE 2.2 WebAPI 和 JavaScript](#net-core-22-webapi-and-javascript)

开发人员通常倾向于避免重复工作。在编码中，我们有 DRY 原则，我喜欢将这一哲学应用到我的编码方式中。这就是为什么我一个接一个地输入获取请求，试图使用我为自己的项目编写的. NET Core Web API 服务时感到沮丧。为什么我不能引用一个 WSDL，然后在我的 JS 中添加一个服务引用呢？

就在那时，我有了写一个库的想法，用你的控制器、参数和结果来构建 JS 代码。生成的代码还应该有完整的 JSDoc 注释，这样您就可以在 Visual Studio 中获得智能感知支持。

我尝试了不同的途径，从 T4 模板到通过 C#中的节点引用构建 JavaScript。最后，我得到了定制的模板和模仿 JavaScript 的分层 C#类结构。

我计划将以下内容添加到库中:
1) C#代码生成器，它也将自动构建一个包含所有代码的 NuGet 包来使用您的 API。
2) C#类和动作级属性，允许你生成定制的 JSDoc 描述注释。
3)自动生成 HTML 页面的功能，允许您为每个控制器动作添加预定义的、计算的和静态的参数和结果，这样您只需打开页面就可以测试您的 API。

我认为结果相当不错，但欢迎任何反馈。

请注意，该库仍处于 alpha 状态，但我已经在不同的项目中使用过它，它看起来相当稳定。这是人们会用的东西吗？

GitHub:[https://github.com/PhilipCoder/WillCoreRequests](https://github.com/PhilipCoder/WillCoreRequests)
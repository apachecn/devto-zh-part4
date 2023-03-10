# 表格很难

> 原文：<https://dev.to/takeshape/forms-are-hard-1hg0>

TLDR；您的应用程序中需要表单吗？有很多选择。我们到 [ShapeForm](https://github.com/takeshape/shape-form) 的旅程！

[https://www.youtube.com/embed/iplY8Le1zK0](https://www.youtube.com/embed/iplY8Le1zK0)

表格难做已经不是什么秘密了。它们给开发人员带来了大量的挫折和困惑。然而，另一件众所周知的事情是，对于您的特定用例来说，表单是非常重要的。表单无处不在，从 Django 到 Rails 到。NET 反应过来，有自己的表单实现。React 甚至将表单列为他们文档中需要搜索的十大概念之一！

有许多用于构建表单的库。如果你在 npm 中搜索“表单”,你会得到成千上万的结果。但是你怎么知道用哪个呢？你应该自己卷吗？在我们寻找完美表单库的过程中，我们尝试了一些顶级选项。

对于我们的表单，我们需要全面的验证、可定制的表单配置和可序列化性。从`redux-form`到`@hapi/joi`到两者一起到 JSON 模式到`react-jsonschema-form`。每个库都有自己的优势和杀手锏，但是没有一个选项能拥有全部…所以最后我们推出了自己的库。

ShapeForm 建立在我们从前面提到的表单库的使用和测试中学到的一切之上。我们在 TakeShape 的 react web 客户端中广泛使用 ShapeForm。以下是我们认为非常有用的功能:

💪JSON 模式验证——轻松共享相同的表单验证前端和后端

⚡️快速纯组件，即使是大型表单也能快速渲染

🗃 Redux /独立状态管理-选择您自己的冒险

💅定制小部件——小部件只是 React 组件

🚀form reducers——对异步验证、数据规范化非常有用，前途无量。

👯‍♀️多部分表单-多个表单组件可以编辑相同的表单数据。适用于复杂布局或多步表单。

在 TakeShape 的网络客户端，几乎所有的东西都是一个表单。这包括我们的内容建模、内容编辑、元数据和登录功能。我们从 ShapeForm 中获得了如此多的用途，以至于我们想把它还给社区。在 [8/20 Reactadephia Meetup](https://www.meetup.com/Reactadelphia/events/263614474/) 上首次宣布，这段代码是从我们的 [React 应用](https://app.takeshape.io/)中新抓取的，并作为 [ShapeForm](https://github.com/takeshape/shape-form) 开源。

如果你喜欢它，而且它对你有效，那太好了！如果你没有或它没有，帮助我们使 ShapeForm 更好-我们知道你会想到一些我们没有想到的东西。
[![Alt Text](img/4a3c7bfa11a0671f054aeaeb90303af9.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--Nu_XItVu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2a3mxng4vtoivswnv1in.png)
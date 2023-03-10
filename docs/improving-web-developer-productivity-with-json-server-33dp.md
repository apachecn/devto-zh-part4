# 用 JSON-Server 提高 Web 开发人员的工作效率

> 原文：<https://dev.to/agrgic16/improving-web-developer-productivity-with-json-server-33dp>

现代 web 开发经常涉及到在独立的前端和后端存储库中工作的团队。这种风格的 web 开发有巨大的优势，包括显示和业务逻辑之间的真正分离。然而，新的挑战也随之而来，因为当团队在独立但相互依赖的代码库区域工作时，协调可能会很困难。在这篇文章中，我将探索一些简单的技术来提高 web 开发人员在这类项目中的工作效率。

# 背景

我在许多项目团队中以这种方式开发 web 应用程序，最大的挑战之一一直是导航前端和后端 web 开发人员之间的依赖关系。企业通常喜欢根据业务增值功能来组织故事和特写。这意味着我们很少有机会为 Sprint 1 规划后端工作，为 Sprint 2 规划前端工作。不幸的是，由于依赖关系，这就是它经常发生的方式——让每个参与的人都有点失望。

我们如何应对这些挑战并提高 web 开发人员的工作效率？我的回答既涉及工艺，也涉及技术。

# 提高 Web 开发人员生产力的流程

1.  就合同达成一致。前端和后端开发人员应该就合同达成一致，并在编写任何代码之前休息 URIs。
2.  将达成一致的合同和 URIs 添加到一些 API 模拟中间件中。
3.  完成前端和后端开发，在中间与商定的合同会面。如果开发人员遇到需要更改合同或 URIs 的问题，他们应该立即向团队提出——在这种情况下，从第 1 步开始重复这个过程。理想情况下，这些都是足够小的疏忽，你不需要从头开始！

当前端和后端都完全开发完成后，将开关切换到您团队的真实 API，并对一切进行冒烟测试。如果您遵循了前面的步骤，那么一切都应该正常工作，并为 QA/部署做好准备！

现在，作为简化步骤的简短列表，事情听起来总是不错的。这里仍然存在一些重大挑战。这个计划要求整个团队对他们正在做的事情和遇到的挑战持开放态度(团队必须快速失败，并迭代)。在技术方面，它还需要一些 API 模拟中间件，其实现远非常识。

# 提高 Web 开发者生产力的技术

在这篇文章的剩余部分，我将带你实现一个很酷的叫做 [JSON-Server](https://github.com/typicode/json-server) 的开源包，这样你就可以模拟一个 API，在开发后端特性的同时开发前端特性。在某些场景中，JSON-Server 可以用几乎零行代码来配置。顾名思义，它可以完全由 JSON 文件驱动。

为了有一个清晰、可用的示例，让我们把重点放在 Vue 应用程序中的实现上。但是请注意，JSON 服务器是框架不可知的。虽然它在任何 JavaScript 项目中都能很好地工作，但它可以很容易地用作独立的模拟 API，允许您从任何客户端向它发出 HTTP 请求。在幕后，JSON Server 是一个 node + express 实现。您将在我的示例中看到 express 服务器已经为我们配置好了，但是如果您查看文档，您还会看到一些展示和定制该实现的好方法，以获得更大的灵活性。

## 实现 JSON-Server

首先，我们需要一个基本的 Vue 应用程序。一个简单的方法是使用 [vue cli 并创建一个](https://cli.vuejs.org/guide/creating-a-project.html#vue-create)。如果你想跳过这一步，你可以[从我的 Github](https://github.com/agrgic16/vue-3-base) 下载一个已经生成的 vue 应用。

现在我们有了 Vue 应用程序，我们需要安装 [json-server](https://github.com/typicode/json-server) 和另一个名为[的包并发](https://github.com/kimmobrunfeldt/concurrently#readme)。

```
npm install json-server --save
npm install concurrently --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们在项目的根目录下添加一个文件夹，位于**/json-server**——这是我们存储所有与 JSON-server 相关的内容的地方。

在 **/json-server** 中，添加一个名为 **db.json** 的文件——这个文件将作为一个轻量级文档数据库。

然后，我们想要向 **db.json** 添加一些模拟数据。我们今天创建的应用程序是一个简单的待办事项列表，任务存储在我们的 json 服务器中。保持简单，我们的任务将只有**名字**、**完成**和 **id** 的字段。

#### db.json
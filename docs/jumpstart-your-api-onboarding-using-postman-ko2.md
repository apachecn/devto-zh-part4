# 使用 Postman 快速启动您的 API

> 原文：<https://dev.to/dwolla/jumpstart-your-api-onboarding-using-postman-ko2>

## 邮递员背景

[Postman](https://www.getpostman.com/) 是快速测试任何 API 的完美工具，并且仍然是我发送和检查对 Dwolla API 的调用的首选。Postman 在过去几年中不断发展，提供了丰富的特性来支持 API 生命周期的每个阶段。无论您是 API 提供者(例如 Dwolla)还是 API 消费者(例如 Dwolla client)，都有一些功能可以帮助您创建/设计 API 以及测试和调试。Postman 提供了一个易于使用的用户界面，不需要你阅读更多的文档就能理解。它只是工作。

在 Dwolla，我们在工程团队中使用 Postman 来快速测试新特性和功能，因为它们被部署到 API 中。由于我们设计了一个贴有白色标签的平台，所以它经常在内部利益相关者演示中使用，以代表向客户展示的界面，帮助他们直观地了解新功能是如何被使用的。我的团队“开发者关系”为所有 Dwolla 客户提供专门的开发者支持。使用 Slack，我们可以直接与集成了 Dwolla API 的开发人员进行交互，提供一对一的协作支持。Postman 是一个关键的调试工具，用于帮助第三方开发人员解决他们在集成过程中可能遇到的任何障碍。

我们在内部使用 Postman 的方式有很多，但我们最看重的是如何用它来帮助板载外部开发者！

## 快速启动到 Dwolla API 的入职培训

为开发人员提供与 Dwolla API 集成的高质量体验需要做很多事情。举几个例子:简化的入职、清晰的文档、设计良好的 API 以及帮助您快速高效地集成 API 的工具。Postman 就是这些工具中的一个，开发者可以用它来更深入地了解一个 API。不久前，我写了一篇关于[为什么你应该使用 Postman](https://discuss.dwolla.com/t/using-postman-to-explore-and-debug-dwollas-v2-api/2758) 的文章，并发布了一个 Dwolla API 中所有可用调用的共享集合。目的是全面了解您可以使用 Dwolla API 做什么，并立即开始进行 API 调用。该集合包括所有端点所需的请求细节，因此您不必花时间摆弄诸如头和了解某个 POST 请求体的结构之类的东西。

[![Dwolla Postman collection](img/2a0144e3930a33d8a38949dabe4e5049.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bTL8sGKs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.dwolla.com/com/prod/20190628103604/PostmanBlogScreenshot1-1.png)

Postman 集合适合所有技能水平的开发人员。如果你是一个初学者，想要了解 API 是如何工作的，那么这个集合并不需要你开箱即用就能看到 API 是如何工作的。如果你是一个有经验的开发人员，想要开发自己的 [API 包装器](https://developers.dwolla.com/pages/sdks.html)，这个集合可以作为一个规范来帮助定义如何实现每个方法。

在这篇文章中，我将进一步扩展集合的使用，并使用 [Postman 的集合运行器](https://learning.getpostman.com/docs/postman/collection_runs/building_workflows)构建一个工作流。注册一个[沙盒账户](https://accounts-sandbox.dwolla.com/sign-up)，获取你的 API 密匙，看看使用 Dwolla ACH 支付 API 汇款有多简单！

## 试试送钱收款跑腿

Dwolla 有各种支付工作流程，针对每个企业的独特使用案例量身定制。每个工作流都包含一组在实际发送付款之前所需的步骤，其中可能包括以下任务:创建客户记录、添加银行帐户和可选的银行验证。汇款工作流程包括发送支出、触发创建收款客户记录和启动付款的请求。使用 Postman 的 collection runner，我们可以[构建一个工作流](https://learning.getpostman.com/docs/postman/collection_runs/building_workflows)来按顺序运行请求，以匹配这个特定的支付流。

按照以下步骤设置汇款收款运行程序:

1.  点击“与邮递员一起运行”按钮，并选择“打开方式”以导入收藏
    [![Run in Postman](img/bacc573d5d93313f71800ddb19032d6a.png)](https://app.getpostman.com/run-collection/d49125f7c64c530895e5#?env%5BDwollaSendMoney.template%5D=W3sia2V5IjoiY2xpZW50X2lkIiwidmFsdWUiOiIiLCJlbmFibGVkIjp0cnVlfSx7ImtleSI6ImNsaWVudF9zZWNyZXQiLCJ2YWx1ZSI6IiIsImVuYWJsZWQiOnRydWV9XQ==)

2.  单击“眼睛”图标编辑 DwollaSendMoney.template 中的环境变量

    [![Edit environment variables](img/bb090048464ec4e83b2ffa8c07c2825a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_KAwXaVO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.dwolla.com/com/prod/20190628105531/PostmanBlogScreenshot2.png)

3.  一旦您的初始变量被更新，您就可以运行集合了！

    [![Run collection](img/aa83ccd71f857fd33327be6de14ca446.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gq8VZITl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.dwolla.com/com/prod/20190628105718/PostmanBlogScreenshot3.png)

**注意:**如果您还没有，请注册一个沙盒帐户以获得您的 client_id 和 client_secret。**你需要设置你的客户端 id 和客户端秘密的初始值和当前值。**

1.  验证环境已设置，然后单击运行

    [![Verify environment and run](img/d6d624cd9eca9fe79db4273f256759a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h9MsGzn9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.dwolla.com/com/prod/20190628105852/PostmanBlogScreenshot4.png)

不用说，我是 Postman 的忠实粉丝，很高兴看到它在 API 领域的进步。关于使用 Postman 或者如何与 Dwolla API 集成的问题？请不要犹豫伸出手来！
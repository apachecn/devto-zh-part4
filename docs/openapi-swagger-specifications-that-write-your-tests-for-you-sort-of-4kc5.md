# 为你编写测试的 OpenAPI (Swagger)规范

> 原文：<https://dev.to/scampiuk/openapi-swagger-specifications-that-write-your-tests-for-you-sort-of-4kc5>

我最近在 NodeJS 中写了一篇文章[使用 OpenAPI (Swagger)文档处理 API 验证，其中讨论了如何将输入验证的工作传递给 OpenAPI 规范。接下来，展示了如何减轻测试负担，并确保您的 API 准确地产生您辛苦记录的输出。](https://medium.com/@Scampiuk/handling-api-validation-with-openapi-swagger-documents-in-nodejs-1f09c133d4d2?source=your_stories_page---------------------------)

[![](img/fc36679375e5528025404f2c3e1b51ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JZbm8nZ3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AdztNVuObpOSvrLaBbE9q2Q.jpeg) 

<figcaption>照片由[保罗·埃施-洛朗](https://unsplash.com/@pinjasaur?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)于 [Unsplash](https://unsplash.com/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

### 依靠 OpenAPI 规范作为单一的事实来源，加快测试速度。

没有什么比 API 文档出错更容易预测的了。

面对维护 API 的压力，很难让文档保持最新。更简单的方法是推送修复或特性，然后更新文档。最终。

如果我在寻找这个问题的解决方案，那我是在撒谎，但是我发现了一个问题，它是另外两个问题的副产品:

1.  使用 API 规范文件进行验证。我们在[https://medium . com/@ Scampiuk/handling-API-validation-with-open API-swagger-documents-in-nodejs-1 f 09 c 133d 4d 2](https://dev.to/scampiuk/handling-api-validation-with-openapi-swagger-documents-in-nodejs-17d0)中对此进行了介绍
2.  使用 API 规范文件进行测试。(本指南)

这个小二重奏意味着 API 规范*必须是最新的，否则你不能通过任何测试。不错吧，胡？*

我们将从尽可能简单的测试应用程序开始:

```
npm install express 
```
# 空手道框架:让我们再次让 API 测试变得伟大

> 原文：<https://dev.to/apium_hub/karate-framework-let-s-make-api-tests-great-again-538b>

我最近谈到了一个叫做 Cypress 的[测试框架。在这篇文章中，我将谈论另一个:一个非常简单的用于 **REST API 自动化**的测试框架，叫做](https://apiumhub.com/tech-blog-barcelona/cypress-framework-swiss-knife-tests/)[空手道](https://github.com/intuit/karate)。为了更好地描述它，下面是来自官方来源的一段非常有描述性的摘录:

> 空手道框架是唯一一个将 API 测试自动化、模拟和性能测试整合到一个统一框架中的开源工具。Cucumber 推广的 BDD 语法是语言中立的，即使对于非程序员也很容易理解。除了强大的 JSON & XML 断言，您还可以并行运行测试以提高速度——这对 HTTP API 测试至关重要。

先来对比一下**空手道**测试与黄瓜[放心](http://rest-assured.io)。

空手道:

[![Karate Test](img/d56ddc367d7805bf74dff1afb7b85368.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QD-kurur--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apiumhub.com/wp-content/uploads/2019/07/karate-test-1024x246.jpg)

黄瓜:+我们必须实现步骤和 POJO:

[![Cucumber Test](img/f44f80accbd7acb353224ca7056be8b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8LeODVnn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apiumhub.com/wp-content/uploads/2019/07/cucumber-test-1024x193.jpg)

放心:

[![Rest Assured](img/0ff16680be2a9a1f810f25b4f56c64b8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uVd_fOus--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apiumhub.com/wp-content/uploads/2019/07/rest-assured-1024x303.jpg)

空手道优势:一处 BDD(相比黄瓜+步骤+商业逻辑+POJO)。如果你需要复杂性，空手道有 Javascript 支持。

对于先决条件使用，空手道建议使用另一个特征文件:

[![Karate Preconditions](img/e572cea265bee0ef002d78e98d67a4f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2FAMwf8Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apiumhub.com/wp-content/uploads/2019/07/karate-preconditions1-1024x248.jpg)

由于空手道是用 java 写的，所以有 Java 集成。

从 API 测试调用 Java 方法，e.x 生成 uniqueId:

[![Generate uniqueId](img/e84ee558fd370c8c7236a83a11640dcc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_1OcP7UA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apiumhub.com/wp-content/uploads/2019/07/generate-uniqueid-1024x137.jpg)

使用空手道的另一个优势是它的断言能力:

[![Karate Assertion Capabilities](img/a1440a7b9f77e7250c6fb75f43766fc3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GNU5CkzX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apiumhub.com/wp-content/uploads/2019/07/karate-assertion-capabilities-1024x334.jpg)

架构验证。用户友好的响应验证格式。将 json 作为变量重用。空手道标记。滴滴涕。类似于 Cucumber，但是与 Cucumber 相比，Cucumber 的变量在表中是静态的，在空手道中我们可以导入一个包含数据的 csv 文件。可以是 api 端点边界测试中的用户实例。

[![Boundary Testing](img/52bf8f83d4bf391ae414378a63db72c6.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--9rkEd8oa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apiumhub.com/wp-content/uploads/2019/07/boundary-testing-1024x330.jpg) [ ![Boundary Testing](img/9c0ebf4af69f35ca08bd3d4ae6bf6118.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4xyN_HSo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apiumhub.com/wp-content/uploads/2019/07/boundary-testing2-1024x417.jpg)

并行执行。使用 Junit5 运行。在这个例子中，我们用标签@regression 运行所有的测试/特性文件。

[![Parallel Execution](img/6a8a822ccef6225fa62021b61946439f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pZqlWdeW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apiumhub.com/wp-content/uploads/2019/07/parallel-execution-1024x275.jpg)

报道。倾城，黄瓜报告。从空手道盒中提出如下报告:

[![Karate API Test](img/4cf9e2d8c32bb6acd5ab03e06ad276bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0dhT64QY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apiumhub.com/wp-content/uploads/2019/07/karate-api-test-1.jpg)

而且我们还可以很容易地整合黄瓜报告或者还倾城 [![😉](img/83c355ff4de5f32d62b7dca10e88e2a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_JWCkMEY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/2.2.1/72x72/1f609.png)

在黄瓜报告的情况下，我们可以获得如下内容:

[![Karate Framework API Test](img/a6492271dcb9449497f621be30bf8f56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D9givmDO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://apiumhub.com/wp-content/uploads/2019/07/karate-api-test-2.jpg)

空手道框架的其他一些很酷的特性:Json 路径、UI 分步调试、与 CI 的集成、内置 ENV 切换器等。

帖子[空手道框架:让 API 测试再次变得伟大](https://apiumhub.com/tech-blog-barcelona/karate-framework-api-tests/)首先出现在[API hub](https://apiumhub.com)上。
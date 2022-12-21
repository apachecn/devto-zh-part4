# em 20 etapas 的 RDBMS

> 原文：<https://dev.to/oieduardorabelo/mudando-de-rdbms-para-dynamodb-em-20-etapas-3ha2>

我[在 Twitter](https://twitter.com/jeremy_daly/status/1137002244157710336) 上张贴了一个话题，对如何从 RDBMS 切换到 DynamoDB 进行了一些思考。有人要求我把它变成博客文章，方便后续。这就是了-我...。最后有几个额外的步骤。祝你好运！😁

[https://twitter.com/jeremy_daly/status/1137002244157710336](https://twitter.com/jeremy_daly/status/1137002244157710336)

我**最近在我的无服务器应用程序中与 DynamoDB 相处了很长时间，所以我想分享我从 RDBMS 迁移到 DynamoDB 的不折不扣的指南。所以这里是:**

 ****Etapa 01**

接受这样一个事实，即 Amazon.com **可以容纳 DynamoDB 网站/零售系统的 90%的工作流**，那么你大概也可以。🤔

**Etapa 02**

创建实体关系模型，就像设计传统关系数据库一样。👩‍💻

**Etapa 03**

创建一个列表**所有**其访问模式。如果“搜索”是访问模式，不用担心，我们会在步骤 17 中处理。😉

**Etapa 04**

将访问标准降低到应用程序用户要求的最低限度。数据分析和管理任务，如 *analytics* 、聚合、任务触发等。，它们很重要，但最终用户可能不需要与您的应用程序进行实时交互。🚫📊

**Etapa 05**

确定用户访问模式是否需要查询**需要重新设计数据。也许答案是否定的。但是，如果要创建 OLAP 应用程序，则 NoSQL 不是一个很好的选择。尝试用另一种技术拍拍你的背。🤷‍♂️**

 ***Etapa 06**

把头放在微波炉里 3 秒钟，或所需的时间，忘掉什么是数据正常化[和](https://en.wikipedia.org/wiki/Third_normal_form)第三正常化。🤤

**Etapa 07**

亚马逊 DynamoDB 的高级设计模式(DAT403-R) 德[里克·霍利汉](https://twitter.com/houlihan_rick)亚马逊 DynamoDB na AWS re:Invent 2017。😯

[https://www.youtube.com/watch?v=jzeKPKpucS0](https://www.youtube.com/watch?v=jzeKPKpucS0)

**Etapa 08**

亚马逊 DynamoDB 深度潜水:DynamoDB 的高级设计模式(DAT401) 德[里克·霍利汉](https://twitter.com/houlihan_rick)亚马逊 DynamoDB na AWS re:Invent 2018。😮

[https://www.youtube.com/watch?v=HaEPXoXVf2k](https://www.youtube.com/watch?v=HaEPXoXVf2k)

**Etapa 09**

把你脑中分散在房间里的所有微小部位都清理干净，然后重新组合，再看一遍，这次是半速。记下来。🤯

**Etapa 10**

阅读 AWS 网站上的指南[**【dynamodb】**](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/best-practices.html)。然后再读一遍。🤓

**Etapa 11**

规划*【dynamodb 表，该表使用过载的索引存储其所有实体，使用 ***【复合排序键】*** (必要时)，添加**当你通过前面的步骤时会更有意义。)。***

 ***Etapa 12**

编写一些示例查询，并根据表的设计测试其访问模式。意识到自己第一次完全错了，呼吸，喝啤酒(或两杯)，然后回到**【步骤 07】**。😞🍺

**Etapa 13**

根据您的**新**表设计测试您的访问模式。特蕾特。再试一次。再测试一次。🤨

**Etapa 14**

重复“T0”步骤 13 ，直到 95%的人确信您的表设计正确。😀

**Etapa 15**

你做到了(嗯，反正第一部分)！庆祝您的成就(也许您还有其他啤酒)，并将 DynamoDB 访问模式连接到 AppSync，或者使用网关 API 创建 API。必要时记得使用**交易 API。😎**

 ****Etapa 16**

测试，测试，再测试。等你测试完再测试。并且一定要写一些测试，这样你就可以一遍又一遍地自动测试它。✅

**Etapa 17**

启用 **DynamoDB Streams** ，并使用它们生成/更新聚合，以及复制用于报告、搜索索引和/或其他应用要求的数据。(Lambda 的功能对于**存储过程**来说是最佳的。)

**Etapa 18**

在云中测试您的**dynamodb streams****以确保数据正确流动，目标资源正确填充/更新。编写一些测试以实现自动化。☁️**

 ****Etapa 19**

交叉手指，在制作中发表。🤞

**Etapa 20**

Lucre！💰

* * *

这显然有点简化，但我希望你开始你的旅程去发现 DynamoDB 的力量。如果你一开始做不到，你就继续吧。他最终会“点击”(除非你把头长时间地放在微波炉里)。祝你好运！👍

# Etapa 奖金

Twitter 上有很多精彩的评论，对 DynamoDB 的其他学习材料提出了建议。如果你想深入 DynamoDB，这里还有一些额外的功能会使你的大脑进一步燃烧。🔥

*   [建立在 DynamoDB | S1 E2 之上——亚马逊 dynamo db NoSQL 数据建模简介](https://www.youtube.com/watch?v=Rmf8mrJ3X2s)

[https://www.youtube.com/watch?v=Rmf8mrJ3X2s](https://www.youtube.com/watch?v=Rmf8mrJ3X2s)

*   [使用 DynamoDB 构建|使用亚马逊 DynamoDB 进行 S1 E3–NoSQL 数据建模](https://www.youtube.com/watch?v=KlhS7hSnFYs)

[https://www.youtube.com/watch?v=KlhS7hSnFYs](https://www.youtube.com/watch?v=KlhS7hSnFYs)

*   [利用 AWS Glue 和 Amazon Athena](https://aws.amazon.com/blogs/database/simplify-amazon-dynamodb-data-extraction-and-analysis-by-using-aws-glue-and-amazon-athena/) 简化 Amazon DynamoDB 的数据提取和分析
*   [如何使用 Amazon Athena](https://aws.amazon.com/blogs/database/how-to-perform-advanced-analytics-and-build-visualizations-of-your-amazon-dynamodb-data-by-using-amazon-athena/) 执行高级分析和创建 Amazon DynamoDB 数据视图
*   [DynamoDB，解说](https://www.dynamodbguide.com) por [亚历克斯·德布里](https://twitter.com/alexbdebrie)
*   [从 DynamoDB 中的单表关系数据库:由](https://www.trek10.com/blog/dynamodb-single-table-relational-modeling/) [Forrest Brazeal](https://twitter.com/forrestbrazeal) 逐步探索

你知道 DynamoDB 的更多不可思议的功能吗？请随意将我上传到 Twitter ！

# [t1【学分】](#cr%C3%A9ditos-%EF%B8%8F)

*   [如何在 20 个简单步骤中从 RDBMS 切换到 dynamo db](https://www.jeremydaly.com/how-to-switch-from-rdbms-to-dynamodb-in-20-easy-steps)，escrito origination e por[Jeremy Daly](https://twitter.com/jeremy_daly)********
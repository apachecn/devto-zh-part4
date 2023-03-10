# 一部分是虫子

> 原文：<https://dev.to/rimt07/bugs-por-todas-partes-1ii>

# 一部分一部分的虫子

## 一种不让生活陷入(虫子)缺陷深渊的策略

### El problemma:

您是否曾遇到过这样的情况:系统如此庞大而复杂，而且测试阶段发现的错误使一个工作组不堪重负？

让开发团队在关注这些的时间里淹死？

我最近遇到了类似的情况。

缺陷的代价很高，要么是因为需要花费大量时间来分析和解决问题，要么是因为不这样做会导致客户或用户的技术债务和/或不满，而且，如果处理不当，可能会给公司带来损失，具体取决于系统环境。所以最好的方法可能是复杂的。因此，我提出以下问题:

∞我们能优化这些错误的注意时间吗？

我们能减缓新虫子的出现吗？

### 解决方案:

实施“零错误策略”。

### 方式:

考虑到公司和开发领域最重要的目标，下面我们将介绍此策略的示例和实施。

[![](img/25eb5ae84c3de84464caa943c0963911.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--06tSpIDW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rm6c19h3m4srpblaifno.png)

考虑到开发团队的结构、系统体系结构和系统的性质，始终追求软件质量和工作方法(并不总是得到很好的应用)，根据各种来源定制以下指南，以及如何实现这一指南；

希望能帮上忙！！

在一个以 azure devops 和. Net 世界为基础的生态系统中，我们拥有多种工具，可帮助我们控制和版本化源代码。但是，它们并不能解决整个问题。在此“平台”中，我们拥有管理任务、要求、缺陷和管理出版物的工具(可能涉及整个软件开发周期)，但有时仍需要根据工作方式和前面几段中提到的考虑因素对其进行定制。

在以下几个段落中，我打算评论如下:

*   自定义工作项目和模板(蓝色 devops)
*   [零错误策略的定义](https://dev.to/rimt07/politica-de-zero-bug-4l44)
*   如何更好地排定缺陷/错误严重性的优先级

我们将努力发现和处理缺陷，将其作为早期感冒，而不是将其工作团队中的一种流行病，从中到大。

**一如既往欢迎你的建设性评论**
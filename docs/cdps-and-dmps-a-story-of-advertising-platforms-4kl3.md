# CDPs 和 DMPs:广告平台的故事

> 原文：<https://dev.to/julienkervizic/cdps-and-dmps-a-story-of-advertising-platforms-4kl3>

[![](img/f6afab72800e294ae6260cdbf2507dc4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xmvF5-76--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Afnr80MmoEuZNQJPtsJzfjA.jpeg)

客户数据平台和数据管理平台是数据驱动的营销技术体系的一部分。它们为营销人员提供了有效瞄准不同人群的工具。

### **客户数据平台**

客户数据平台是与给定客户相关的所有内部属性和事件的数据存储。它旨在提供一种方法来统一跨多个数据源的数据，这些数据源可能具有不同类型的客户标识符，因此需要提供一种方法来解析身份。

#### CDP 的使用案例:

**单一事实来源:**客户平台的目标是充当客户数据的单一事实来源，提供跨不同接触点和身份的 360 度客户行为视图。

它还支持对客户的输入和数据模型进行结构化和标准化，例如，使用 mParticle 的[商业事件](https://docs.mparticle.com/developers/server/json-reference/#commerce_event)将不同的产品行为构建到相同的模式中。除了标准化组件之外，CDP 在数据集成阶段的部分职责也是通过清理和消除数据杜平来提供更好的数据质量。

**广告:**由于 CDP 能够从客户互动的不同接触点收集数据，并解析他们的身份。它还能够协调通过不同通信渠道与客户的通信。

**实现个性化:** [个性化](https://medium.com/analytics-and-data/principles-of-e-commerce-personalization-4feb6de4b6e)可通过使用客户数据平台实现最高水平。客户数据平台允许通过 a) [属性检索](https://community.tealiumiq.com/t5/Tealium-API/Visitor-Lookup-API/ta-p/22859#toc-hId-1330156159) b) [细分创建](https://www.qubit.com/customer-segmentation/) c) [机器学习得分](https://relay42.com/resources/blog/optimising-customer-journeys-with-ai)的方式，从合并的身份创建和利用数据。这些数据点可用于推动广告活动、网站推荐或在整个用户旅程中实现个性化。

**简化集成:**客户数据平台的作用之一是促进不同数据源的集成，包括入站和出站。客户数据平台需要能够集成 web 分析、电子商务数据、电子邮件行为等数据源，并导出到可以利用广告平台、营销自动化工具或数据仓库等数据的不同端点。

#### CDP 列表

有相当多的 CDP 供应商，通常每个供应商都有不同的侧重点。

*   源自 **标签管理系统**提供商的 CDP，如 [Tealium](https://medium.com/analytics-and-data/tealiumiq-tms-3195095cab66) 或[ensigne](https://medium.com/analytics-and-data/google-analytics-with-ensighten-tms-cd7e1933fc89)。这些系统倾向于通过利用它们用于标签集成的公共数据模型来转向客户简档。
*   **纯游戏导向:**如 [*段*](https://segment.com) *，*为数不多的提供免费开发者试用版的 CDP， [*mParticle*](https://www.mparticle.com/) *，* CDP 专注于提供数据集成，提供统一的数据模型，将不同类型的事件如商务事件摄取到一个通用的数据模型中，并将其导出到不同的端点。
*   **面向营销活动管理:**是一个专注于营销活动管理、客户旅程和丰富第二方数据资料的 CDP， [*Agile One*](https://www.agile-one.com/) *一个兼具营销活动和分析功能的* CDP 或 [*SessionM*](https://www.sessionm.com/) 一个专注于提供忠诚度管理的 CDP。
*   **面向个性化:** [*量子位*](https://qubit.com) *，*虽然它并不将自己作为 CDP 来销售，而是作为一个个性化平台，但它与他们共享许多功能，例如跨身份删除客户重复数据的能力、在用户级别存储数据、细分受众的能力以及与其他平台集成的一些可能性(例如:Salesforce 营销云)。
*   **开源:** [*Unomi*](https://unomi.apache.org/) 是一个开源 CDP，它的一些缺点是缺乏 UI，需要托管它以及缺乏广泛的集成生态系统。

#### **须知**

将 CDP 视为客户的黄金记录需要有所保留，摄取的数据通常与属性一起持久化，例如，产品信息通常与触发的事件同时持久化。大多数 CDP 的后端通常不允许维护维度(如产品类别维度)所必需的合并活动。

### **【DMP】数据管理平台**

DMP 的目的是什么——数据管理平台的目的是收集、丰富数据和管理数据，用于数字营销。DMP 的主要价值之一是能够在不同的营销渠道中提供一致的体验。

#### 数据收集和浓缩:

DMP 从客户的第一方来源收集数据，例如从访问其网站的用户那里收集数据。从第二方来源，即:通过协作获得的数据，这可以是用户访问网站时可以识别的脸书活动的目标设置，以及从第三方来源，从其他活动获得的数据。

DMP 的核心是匿名用户身份的概念，通常通过 cookie ids 来识别，这些 cookie 可以通过规则库系统或概率匹配来匹配。

#### DMP 的用例:

广告:DMPs 的主要目的是提供一个有凝聚力的目标受众。这是通过建立细分市场和为不同类型的人物角色创建不同的细分市场来实现的。DMP 通常提供不同类型段:

*   **静态分段:**分段基于访问者属性的快照运行一次。
*   **适应性细分:**根据提供给平台的数据不断更新细分。例如，自适应分段可以是“网站访问者 30 天”
*   从机器学习模型中获得的片段用于**相似建模**。

然后可以将这些片段导出到 DSP 或其他激活通道。

**受众洞察** : DMPs 还提供对客户行为模式的洞察，并通过利用第三方提供的信息了解更多关于客户的信息。

#### DMP 列表

*   [*sales force DMP*](https://www.salesforce.com/products/marketing-cloud/data-management/)(Krux):sales force 将 Krux 整合为其营销云平台的一部分，提供了一种在您的网站上锁定“未知”访问者的方法，并通过第三方数据增强您对他们的信息。
*   [*Adobe 受众管理器*](https://www.adobe.com/analytics/audience-manager.html) :是 Adobe 的 DMP，很好地集成到 Adobe 营销云，它使营销人员能够更好地掌握不同的受众群体，与他们的广告或网站互动。
*   [*Mediamath*](https://www.mediamath.com/) :介于 DMP 和 DSP 解决方案之间的独立广告技术供应商，受益于超过 6 亿美元的融资。
*   Oracle BlueKai :甲骨文的 DMP，拥有超过 200 家媒体合作伙伴。

### 总结起来

从本质上来说，CDP 和 DMP 在功能上有重叠，但也有一些差异，这些差异主要源于它们对身份的处理。CDP 在功能上也与其他系统重叠，如标签管理、主数据管理和营销自动化工具。

* * *
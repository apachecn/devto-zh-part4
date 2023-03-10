# 我们在里斯本大众软件开发中心的质量方法

> 原文：<https://dev.to/lsoares/our-approach-to-quality-in-volkswagen-software-dev-center-lisbon-141k>

#### 确保我们在大众 SDC 制造的高要求软件产品的质量:LX 并不容易。仅仅说我们是敏捷的是不够的，所以我将展示确保所需标准得到满足的软硬因素。

<figure>[![](img/37e62e424c727b5b60841f819963c02b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jVkJ7IV_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A-Nrp3ycbKNGWZh2DnkgUEQ.jpeg) 

<figcaption>[大众 SDC:LX](https://www.vwds.pt/sdclx.html)</figcaption>

</figure>

作为免责声明，这仅仅是我的观点。

### 文化

*   **团队成员是招聘评估阶段的一部分**，这极大地提高了兼容性，并确保了文化和方法的连续性。
*   **批判性思维**受到高度赞赏，每个人都可以自由挑战已定义的事物和其他事物(无论角色如何)。这是由我们的非判断环境支持的。
*   我们依靠科学的方法来解决问题，通过**设定假说和假设，并寻求证实或反驳它们**。
*   **团队授权培养所有权**:由于团队负责产品(构建和操作)，每个人都更加专注于产品。我们有一个扁平的结构，出于自治的原因，团队是平衡的(项目经理、设计师和开发人员)。
*   上下文切换是昂贵的，所以**聚焦**对我们的工作方式是必不可少的。例如，你一次只能做一个产品。
*   关于发展良好做法的讨论是受欢迎的、受重视的和常见的。
*   我们提倡团队甚至办公室之间的轮换(我们为办公室之间的人员交流开展项目)。这允许分享想法和解决方案，进行比较和学习，并质疑已实施的习惯。
*   **反馈文化**促进个人/团队进步(团队、产品和办公室回顾、个人反馈等)。).每个人都在反思并不断进步。
*   保持透明度和 T2 幸福感，让每个人都感觉自己是办公室的一部分。快乐团队每周与每个人进行办公室同步，并定期组织社交活动。
*   一种不断学习的文化正在形成。分享有趣的文章和其他网络资源是很常见的。我们有一个图书馆和免费的在线课程。我们组织、参加和主办活动。我们有每周一次的内部会议，任何人都可以在会上发表意见。

### 做法

*   每一个潜在的产品都从一个范围界定会议开始，以定义要解决的问题的边界；接下来是发现/框架阶段，以更好地理解问题并验证我们是否应该继续。如果是这样，我们做一个初始阶段来开发一个 [MVP](https://en.wikipedia.org/wiki/Minimum_viable_product) ，这样我们就可以在真实环境中快速测试它。这确保我们做最少的工作来快速验证它，并尽快得到反馈([精益开发](https://en.wikipedia.org/wiki/Lean_software_development))。
*   遵循以用户为中心的理念。**backlog 中的每一个特性都以清晰的方式写出来，为用户带来** [**的价值**](https://www.goodreads.com/book/show/13436116-lean-ux)；这解释了你不会发现故事被后端和前端分割——我们总是“垂直”分割。
*   我们在团队和办公室内做小实验，尝试可以改进我们工作的新事物(例如，关于我们的实践)。每个人都可以提出改进意见。
*   大多数文档都是以协作的方式生成的，并且会快速迭代。这是通过使用**协作工具**实现的，如数字白板、文字处理器、问题跟踪器等。
*   通过不断地[配对](https://dev.to/lsoares/emotions-management-when-pair-programming-18b8-temp-slug-3294279)，我们正在运行**持续的实时代码评审**，与解耦/异步代码评审相反；反馈是即时的，有语境的，因此更有价值。
*   结对保证了**知识共享**，这是因为结对的本质，也是因为结对的日常轮换。因此，所有团队成员都知道代码库/架构，并且不太依赖个人(“无英雄”文化)。设计师和产品经理(pm)也可以结对合作，**促进角色之间的知识共享**。
*   **决策经过辩论**提高了决策的质量。默认情况下，他们是成对完成的，但如果他们觉得有必要，他们会联系团队甚至其他团队进行讨论。
*   除了所有的自动质量检查之外，团队还根据每个特性进行手动测试:开发人员验证故事，项目经理/设计师在接近生产的环境中接受它们。
*   **持续改进**发生在代码库级别。重构通常在处理一个故事时进行。其他重构和技术债务被跟踪，因此它们不会丢失(任何团队成员都可以将它们添加到 backlog 中)。保持代码的可维护性是核心价值。
*   志愿主持人**提高会议的质量和成果**。他们确保会议有时间限制且重点突出。我们从发散开始，最后收敛。
*   测试驱动开发 ( **TDD** )确保一个更好的软件设计和代码最终被记录和测试。因为我们关注的是行为(BDD ),所以实现直接来源于用户需求。
*   整个团队(可能还有其他人)经常被邀请参加由设计师组织的**设计会议**(例如设计一个特性)。该反馈被考虑在内，并且设计被迭代。

### 技术

*   **开发人员是全栈式的**,从上到下、从开始到结束促进特性知识/所有权，以及跨多种技术的技能集。
*   人们对技术有一种普遍的热情(这几乎是一个要求)，但我们有办法确保它不会影响我们的决策，所以我们把注意力放在用户身上。
*   团队可以自由选择语言、运行时和框架，并试图在选择时做出明智的决定。
*   对于每种语言和运行时，我们选择并设置我们的 ide、编辑器、实用程序和插件，这样我们就可以得到代码警告、提示、验证、自动完成等。这些总是保持最新的。
*   **注意警告**:代码编辑器、日志或其他中的每一个警告都是某些事情的征兆，所以我们总是试图修复它们。
*   **静态代码分析**:根据代码约定和其他关注点自动分析代码。
*   **版本检查**:我们确保使用最新版本的运行时、框架和库。这减少了诸如安全修复之类的错误数量。
*   **单元测试**、**集成测试**、**端到端(E2E)测试**默认对所有软件组件进行，测试多个层次和组合。因为那些是在 TDD 中开发的，所以当重构或添加特性时，我们感到非常安全。所有的测试套件都是按代码推送运行的。
*   **持续交付/集成(CD/CI)** :特性被推送到主分支(基于主干的开发)并自动部署到预生产。这样，我们就不必跟踪大量的特性分支和发布。CI 渠道在电视上持续可见。
*   **CI 中的不同环境**在进入生产之前:如果任何测试失败，代码都不会被部署。这也为在生产前发现问题提供了更多的机会，因为所有团队成员、主管和 POs 都使用它们。

[更多信息](https://vwds.pt/sdclx.html)。
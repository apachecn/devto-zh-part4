# 如何驯服事件驱动的微服务

> 原文：<https://dev.to/berndruecker/how-to-tame-event-driven-microservices-2chh>

*这篇博文最初发表于信息世界* *的* [*。我也在最近的*](https://www.infoworld.com/article/3391592/how-to-tame-event-driven-microservices.html)[*QCon NYC*](https://qconnewyork.com/ny2019/presentation/opportunities-and-pitfalls-event-driven-utopia)*(*[*幻灯片*](https://berndruecker.io/opportunities-and-pitfalls-of-event-driven-utopia/) *)做了关于这个话题的演讲。*

现代微服务架构是事件驱动的、反应式的和编排的(与通过编排器集中控制相反)。这使得它们松散耦合，易于更改。对吗？

#### TL；大卫:没那么容易！你将面临理解和管理事件流程的挑战。

在本文中，我总结了我在精心设计的微服务方面的经验，并探讨了这种方法的各种后果和挑战。我用的是典型的业务例子**客户入职**(根据行业不同，这可能是你熟悉的**开户**)。在下面的图片中，我使用 **Apache Kafka** 来表示事件总线，但是如果您运行不同的堆栈，请不要担心。同样的概念仍然适用。

### 精心设计的微服务

让我们假设以下服务和事件构成了您精心设计的系统:

<figure>[![](img/46d1784578337ad94f3211abd00255cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o5ACOarP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/763/1%2ACsuyWk9Lv6r2NOpRhRASnw.png) 

<figcaption>事件驱动的微服务形成编排</figcaption>

</figure>

以下问题说明了这种方法的主要挑战:

*   如何**改变**事件的流程？
*   如何**避免迷失**心流？你是如何保持对它的理解的？
*   如何**管理总体流程的 SLA 和弹性**？你怎么知道什么东西卡住了？怎么能重试呢？你怎么能升级？
*   如何**避免有线耦合**(例如，信用检查必须了解客户注册)？

让我们检查前两个挑战，我们将在适当的时候解决最后两个。

### 改变事件的流程

假设您需要添加犯罪记录检查。精心设计的系统的想法通常是，你可以添加微服务，而无需改变其他任何东西。实际上，您可以添加一个犯罪检查服务，它可以很容易地对某些事件做出反应。但是为了理解这个结果，你还必须至少调整客户服务:

<figure>[![](img/3b188c4e744e543f7d9110538a2e7132.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZsjUWbgC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/763/1%2AzW-KZ8XzB1MIylRIGvYh7g.png) 

<figcaption>你要换两个服务才能加一个犯罪检查</figcaption>

</figure>

因此，您设计的系统并不像您预期的那样松散耦合。

通常，人们会认为上面的事件流是有缺陷的，你必须改进它来解决这个问题。因此，让我们尝试另一种流程:

<figure>[![](img/a75ea59365cf6d8209f5eb8e7dc729b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Dn-D4a0I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/763/1%2AfGgo96zW524X9hMzxYnVbw.png) 

<figcaption>这种另类的事件流仍然需要您更改两个服务来添加罪犯检查。</figcaption>

</figure>

现在所有服务并行工作，客户服务负责拾取所有事件。但是您仍然需要更改两个服务，因为您希望在客户服务中处理犯罪检查的结果。

不要误解我。我认为这种变化是不可避免的。我的观点是，在你的架构中，你无法避免某种程度的耦合。构建一个事件驱动的系统不会神奇地把这些需求赶走。

### 失明？

下一个挑战是了解您的架构中正在发生什么。事件驱动的系统将会有"**新兴行为**"，这些行为只会在运行时被体验到。你不能通过静态代码分析来理解这种行为。[斯蒂芬·蒂尔科夫将这种](https://youtu.be/RsyOkifmamI?t=1736)行为命名为“刚刚到底发生了什么”，[乔希·伍尔夫写道](https://medium.com/@sitapati/node-js-client-for-zeebe-microservices-orchestration-engine-72287e4c7d94)“我们正在取代的系统使用复杂的点对点编排，需要跨多个代码库进行推理才能理解。”Martin Fowler 在[中对这一挑战做了最好的描述，“你所说的‘事件驱动’是什么意思？”](https://martinfowler.com/articles/201701-event-driven.html):

> 事件通知很好，因为它意味着低水平的耦合，并且设置起来非常简单。但是，如果真的有一个逻辑流运行在各种事件通知上，这就有问题了。问题是很难看到这样的流程，因为它在任何程序文本中都不明确。通常，找出这种流量的唯一方法是监控一个实时系统。这使得调试和修改这样的流变得困难。危险在于，很容易用事件通知来制造良好解耦的系统，而没有意识到你忽略了更大规模的流程，从而在未来几年给自己带来麻烦。模式还是很有用的，但是你要小心陷阱。

<figure>[![](img/99ff5f07483a6f6eee29ddf363653c6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l2lZatRD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1005/1%2ATmmp7lMWLI2MBljbvs0xew.png) 

<figcaption>[监控和管理跨协作微服务的工作流](https://www.infoq.com/articles/monitor-workflow-collaborating-microservices) on InfoQ</figcaption>

</figure>

在最近一篇关于 InfoQ 的文章中(参见[“跨协作微服务的工作流监控和管理”](https://www.infoq.com/articles/monitor-workflow-collaborating-microservices))，我总结了一些恢复监督的选项:

1.  分布式追踪(例如 Zipkin 或 Jaeger)
2.  数据湖或分析工具(如弹性工具)
3.  流程挖掘(例如 ProM)
4.  使用工作流程自动化进行流程跟踪(例如 Camunda)

正如本文所讨论的，分布式跟踪太技术性了，错过了业务视角，并且不能告诉您任何关于停滞流程中的后续步骤的信息。因此，您可能不知道手边的错误阻碍了什么，以及如何继续前进。弹性的和类似的工具是很棒的，但是需要一些努力来设置和额外的努力来提供一个合适的商业视角。流程挖掘通常侧重于日志文件分析，而不是事件驱动的架构。过程跟踪值得在这里讨论一下。

#### 使用工作流自动化进行流程跟踪

您可以附加自己的组件来读取所有事件并跟踪某些行为。

<figure>[![](img/09f316b4b9c37aa7e8c274ae2dc8fa16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b1AvLqu6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/782/1%2AS34pMlKWLmt7ST7K5g6fvQ.png) 

<figcaption>通过向事件总线</figcaption>

</figure>

附加一些跟踪服务来跟踪流

行为可能意味着单个服务的“状态变化”:

<figure>[![](img/78558629da979ad88cfc07a8616759de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y5YXjtHn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/977/1%2AdAljzs1fS-wFJOFi1O97Hw.png) 

<figcaption>捕捉 BPMN 州的变化——一种标准化的流动语言</figcaption>

</figure>

当您处于这一点时，跟踪整个事件流可能是有意义的:

<figure>[![](img/8ccc74579163fd5bde47df955d002772.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6XqX9Ovv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/978/1%2ApFosbx5TGBvnWOW3zaT1mA.png) 

<figcaption>描述 BPMN 端到端的流量进行跟踪</figcaption>

</figure>

这已经允许您使用工作流引擎中的工具来监控 SLA，例如:

<figure>[![](img/6b266c37359a515d179c47c726a3657a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CP4dYWp0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/627/1%2A8PFD4_RuZeLq64FZ8vnw0A.png) 

<figcaption>此屏幕截图展示了如何使用工作流引擎的功能来监控 SLA 或分析流程</figcaption>

</figure>

但是，流程跟踪的另一个巨大优势是，您可以开始对某些行为采取行动，比如超时:

<figure>[![](img/36fbae36e93c2e614ee11001df9a55f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RdQ7b7LK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/975/1%2ACeRDhSqoGUK1ODwaappcHw.png) 

<figcaption>允许在特定情况下采取行动(如超时)</figcaption>

</figure>

现在这提出了一个有趣的问题:如何触发重试？在上述架构中，这可能意味着重新发送启动流的事件(需要地址检查)。但是没有上下文，这很难说。

如果我们着眼于跟踪整个事件流，我们会开始理解上下文，并可能对重试有更好的认识:

<figure>[![](img/fa1a28a532b60750794c28c61b5cdf8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LQ3TGh8B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/982/1%2AH0kaWDLSKE4nayhqYJ8YaA.png) 

<figcaption>在本例中，我们并行等待地址和信用检查，并允许个人反应</figcaption>

</figure>

如果注册时间太长，您甚至可以添加一些总超时来取消注册:

<figure>[![](img/653748fe93fd8be7f4780c41e8fb974c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OEdloIVC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/980/1%2AgmkUL72AJK8yRnXa_7MLXQ.png) 

<figcaption>我们取消注册，并在 4 小时后做其他事情——与我们在流程中的位置无关</figcaption>

</figure>

在 Kafka Summit San Francisco 2018(参见[“使用 Kafka 和 Zeebe 监控和编排您的微服务场景”](https://kafka-summit.org/sessions/monitoring-orchestration-microservices-landscape/))上关于流程跟踪的演讲中，我展示了这种零售流程的一个具体示例，即代码中的[。](https://github.com/berndruecker/flowing-retail/tree/master/kafka/java/choreography-alternative/zeebe-track)

取消甚至可能需要更复杂的逻辑，比如撤销某些活动。我在“传奇:如何在没有两阶段提交的情况下实现复杂的业务事务”中描述了这样一个场景

这些是走向编排的第一步，因为这个工作流会主动发送事件，我们稍后将对此进行研究。

### 管理 SLA、弹性和整体流程

但是首先让我们看看这个跟踪工作流的治理。谁拥有它，它部署在哪里？

人们认为工作流引擎是集中式的重量级工具。但这不再总是正确的。在微服务架构中，工作流引擎是在一个微服务中使用的库。(我在 QCon London 的[“分布式系统中的复杂事件流”](https://berndruecker.io/complex-event-flows-in-distributed-systems/)中讲过这个；参见[“避免 BPM 块”](https://blog.bernd-ruecker.com/avoiding-the-bpm-monolith-when-using-bounded-contexts-d86be6308d8))

<figure>[![](img/c4bd457e1d9921a7969a9614aea342e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--95bZLQYM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/292/1%2AbDH9gAyOKqqCHJnxyMxwxw.png) 

<figcaption>工作流引擎是一个微服务的实现细节，没有集中</figcaption>

</figure>

在我的[“微服务集成的 3 个常见陷阱](https://berndruecker.io/3-pitfalls-in-microservice-integration/)”演讲中，我给出了一个例子(Java 和 Spring Boot)，在这里，我使用 Camunda 工作流引擎只是为了进行有状态重试(GitHub 上的[源代码)。它真的很轻巧，很好用。不需要对微服务领域陌生的中央引擎或“编排”流程。](https://github.com/berndruecker/flowing-retail/blob/master/rest/java/payment-camunda/src/main/java/io/flowing/retail/payment/resthacks/PaymentRestHacksControllerV3.java#L37)

为了确定谁可以拥有这样的跟踪工作流，您需要**在您的组织中找到一些拥有新客户的端到端业务能力**的人。你的公司应该有这样的人！一个想要一个流畅的入职流程，关心满足 SLA 或要求，并且知道等待一定重试时间是可接受的，以及如果错过 SLA 会发生什么的人。

在我们的示例中，我可以看到客户微服务承担这一责任，或者您引入一些新的微服务:

<figure>[![](img/8e83d1343de349400cccdf84f05be543.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gSdHFWUO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/828/1%2ApZ1KAlADDL0J5Nik0S6FcA.png) 

<figcaption>流程跟踪可以由单独的微服务拥有。工作流引擎是服务</figcaption>

</figure>

实现细节

### 通过事件和命令耦合

我想加入另一个重要的想法。在上面的例子中，我曾经使用了一个名为“需要地址检查”的事件我见过很多这样的事件。但实际上，这不是一个事件！这是一个伪装的命令。一个事件通常让某人知道某事发生了。这是一个广播节目，你不在乎谁来接收它。

当您发出诸如“需要地址检查”这样的命令时，您并没有告诉世界发生了什么，而是希望地址检查器为您检查一些东西。最好用检查地址命令来明确这一点。

在我看来，命令并不邪恶，也不会增加耦合。两个服务之间的每一次通信都需要某种程度的耦合，这样才能有效。根据手头的问题，在一端实现耦合可能比在另一端更合适。

<figure>[![](img/7d0c72f0654b2f75a72136071fddadad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qvnBpZR9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/645/1%2AVES2K578ivKlZ98G4pdQyQ.png) 

<figcaption>每个通信都需要某种程度的耦合，根据手头的问题，在一方内部实现耦合可能比在另一方内部实现耦合更合适</figcaption>

</figure>

### 微服务内的编排

当您向组合中添加命令时，您最终会得到拥有一些编排逻辑的客户入门服务。但是这很自然，因为这个服务已经负责围绕整个流程的重要决策，比如对超时和错误的反应。

<figure>[![](img/8ae4465a419cde7352139310431ef45a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k1r6zQ9y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/763/1%2A8Wt5LOEa2OVYQ7ZE5QMSYA.png) 

<figcaption>在微服务中包含编排在适当的时候适当地使用命令可以让我们平衡编排和编排</figcaption>

</figure>

重要的是要认识到这确实是一幅与“旧的”SOA 和 BPM 时代不同的画面。没有中央工作流引擎，也没有分离的编排逻辑。相反，我们简单地定义了具有明确业务职责的微服务。有时这种责任涉及状态处理，因此工作流引擎就派上了用场。有时候，命令是一种更明智的耦合方式，所以在这种情况下，微服务也会进行一些编排。同样，在 GitHub 上可以找到源代码中这种方法的[示例。](https://github.com/berndruecker/flowing-retail/)

### 比较耦合程度

让我们重新审视一下针对不同方法引入额外犯罪记录检查的变化:

[![](img/847ff871edf17970c9d90df342ae42b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VOSA1-bN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/832/1%2AqRs3vRGM3OjK36xYek01Fg.png)

*   在精心设计的场景中，您可以独立于任何其他更改来添加和部署犯罪检查。例如，它将侦听“注册请求”事件。但是，在您的客户服务等待结果并将其用于最终决策之前，不会使用该结果。这意味着您也必须更改并重新部署该服务。
*   在编排的场景中，您可以独立于任何其他更改来添加和部署犯罪检查。检查服务不需要知道关于注册的任何事情；只要提供一个合适的 API，就可以用了。为了使用它，您必须调整客户登机服务以调用它，例如通过命令消息，然后等待结果事件。

变化挺像的，耦合度其实是一样的。作为一个额外的奖励，精心安排的方法对我来说似乎更自然，因为刑事检查现在真的像一个内部服务提供商，不一定知道它的所有客户。

### 总结

一个好的架构需要平衡编排和编排。这并不总是容易做到的，尤其是当编排经常被认为是构建灵活系统时应该避免的事情。但是精心设计的系统的经验清楚地显示了这些系统的两个主要挑战:理解它们的行为的困难和改变它们的困难。我希望上面概述的例子能帮助你理解我的思路，到目前为止，它在许多现代微服务架构的真实客户项目中运行良好。

[*贝恩德·吕克尔*](http://berndruecker.io) *是* [*卡蒙达*](http://camunda.com/) *的联合创始人兼首席技术专家。我对开发人员友好的工作流自动化技术充满热情。关注我* [*推特*](http://twitter.com/berndruecker/) *。一如既往，我喜欢得到您的反馈。评论下面或者* [*给我发邮件*](http://bernd.ruecker@camunda.com) *。*

* * *
# 使用 Microsoft 应用中心开始使用数据和身份验证

> 原文：<https://dev.to/mikecodesdotnet/getting-started-with-data-and-auth-using-microsoft-app-center-26lm>

[![Screenshot 2019-06-10 at 08.56.05.png](img/0a41cc5c986c9e65e371056066e16411.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7f_2Q9Ge--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/mikecodes.net/wp-content/uploads/2019/06/Screenshot-2019-06-10-at-08.56.05.png%3Fw%3D750%26ssl%3D1)

在我为 Xamarin 工作的第一周，我从伦敦飞到德克萨斯州奥斯汀，以帮助支持 Xamarin Evolve 2013。像大多数会议一样，我们有赞助商，其中包括一家名为 Parse 的初创公司。Parse 是一个诞生于 2011 年的后端，允许移动开发者轻松地将云存储、推送通知和认证添加到他们的应用程序中。我立刻被这项技术吸引住了，并开始使用 parse 后端构建示例应用程序来演示 Xamarin。

Parse 于 2013 年被脸书收购，直到 2016 年才被关闭。谢天谢地，脸书开源了 Parse，允许用户使用我们的 [Parse 服务器模板将他们的 Parse 后端迁移到云提供商，比如 Azure。](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.ParseServer?WT.mc_id=appcentermbaasintro-devto-mijam)

Parse 不再有维护者对很多用户来说都是个问题，谁会想把新应用押在无人维护的服务上呢？我决定我开发的任何新应用都不依赖于 Parse，而是需要找到一个替代方案。作为一名. NET 开发者，使用微软的 Azure 移动应用服务是有意义的，它由一个基于. NET 框架的 web API 项目和一个 T2 客户端软件开发工具包组成。

拥抱 Azure 移动应用让我在 Visual Studio 中花了更多时间编写通用 Web API，并拓宽了我在 Web API 开发方面的经验。这些技能使我能够放弃预先构建的解决方案，开始推出自己的解决方案，正如我与我尊敬的同事 Robin-Manuel 一起创建的[移动云研讨会](https://github.com/microsoft/app-innovation-workshop)中所看到的那样。

虽然滚动后端提供了无与伦比的灵活性，但开发、测试和维护也是一项不小的任务，这最终分散了我构建移动应用程序的核心目标。自从 2016 年从 Parse 迁移以来，我还没有达到开发云连接应用程序的生产力水平。

## 应用中心

随着微软[应用中心](https://visualstudio.microsoft.com/app-center/?WT.mc_id=appcentermbaasintro-devto-mijam)的最新更新，这一切都改变了。如果你没有听说过 App Center，它是 HockeyApp 或 Xamarin Insights 的替代品。这是我们专门为移动开发者设计的自动化构建、测试和分析平台。

事实证明，应用中心对于构建更好的应用程序非常有价值，但它不是移动开发的“一站式商店”，我必须自己开发数据和身份验证的后端。今年早些时候，随着身份验证和数据存储特性的增加，这种情况发生了变化。

### 认证

[![Screenshot 2019-05-23 at 12.24.18.png](img/f4e34b7cd43e407680d3da05471b86c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lLw6ZeSg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/mikecodes.net/wp-content/uploads/2019/06/Screenshot-2019-05-23-at-12.24.18.png%3Fw%3D750%26ssl%3D1)

身份验证是我喜欢避免的特性之一，因为它既不令人兴奋又容易出错。正是因为这个原因，我从不推出自己的认证解决方案，而是依赖第三方，如 T2 的 Active Directory 或 T4 的 Auth0 和 T5。

当我第一次开始使用 Azure Mobile Apps 及其配套的 mobile SDK 时，我只需要几行代码就可以验证用户，这使得我可以轻松地将它添加到我的演示中。我非常高兴地说，新的移动应用软件开发工具包使它变得一样简单！它通过包装使用 OAuth 2.0 实现认证的[微软认证库](https://docs.microsoft.com/en-us/azure/active-directory/develop/msal-overview?WT.mc_id=appcentermbaasintro-devto-mijam) (MSAL)来做到这一点。

[![token-exchange-service](img/c35db51dde354a5a63ea334e01de6d60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vtqkooRW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/mikecodes.net/wp-content/uploads/2019/06/token-exchange-service.png%3Fw%3D750%26ssl%3D1)

认证过程由 [Active Directory B2C](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview?WT.mc_id=appcentermbaasintro-devto-mijam) (企业对消费者)提供支持，这使得设置非常简单！它还可以使用各种身份提供商，如谷歌、Twitter、脸书和微软，以及与第三方提供商集成。它拥有所有标准的 B2C 功能，例如使用机器学习来检测可疑行为和多因素认证选项。

一旦用户通过身份验证，他们就可以使用只有他们才能看到的应用中心数据创建文档。

### 数据

[![Screenshot 2019-05-31 at 17.26.37](img/1278f167b4fab3a739bbd860cbc1ba11.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3av5i9oO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/mikecodes.net/wp-content/uploads/2019/06/Screenshot-2019-05-31-at-17.26.37.png%3Fw%3D750%26ssl%3D1)

App Center 和 Azure 移动应用之间最大的区别是我们如何建模和存储数据。对于旧的 Azure 移动应用，我们可以在微软 Azure SQL 或测试用的 SQLite 数据库之间进行选择。我们唯一的选择是关系数据库，如果您有一个非常适合规范化的模式，这是很好的选择。

App Center 使用 [Cosmos DB](https://azure.microsoft.com/en-us/services/cosmos-db/?WT.mc_id=appcentermbaasintro-devto-mijam) 作为其底层数据存储服务。这是我们的云优先、全球规模的 NoSQL 数据库产品。Cosmos DB 让我们忘记了数据库伸缩，因为它承诺在您部署数据的所有区域实现低延迟和高可用性。

[![data-architecture](img/ddb07b1e5e7ea022651ab42839f83469.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9PCQuYyL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/mikecodes.net/wp-content/uploads/2019/06/data-architecture.png%3Fw%3D750%26ssl%3D1)

### 分区数据

App Center 只使用一个集合，这有助于降低成本，因为我们为每个集合付费，即使在使用 Cosmos DB 时是空的。在用于 App Center 的单个集合中，您可以定义两种类型的文档，私有和公共。

私有文档由用户数据组成，允许经过身份验证的用户拥有读写权限来编辑内容。

公共文档只允许读取访问(使用 App Center SDK ),以避免用户能够覆盖全局数据。

[![data-partitions](img/f1181b1f323406dc1e81396a9121c3f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rlXH3trp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/mikecodes.net/wp-content/uploads/2019/06/data-partitions.png%3Fw%3D750%26ssl%3D1)

### 离线

我最喜欢使用 Azure 移动应用的一个特点是它支持客户端和后端数据的离线同步。离线同步不仅允许应用程序离线工作，还让它们在线时看起来更有性能。

App Center 支持离线读写，但默认情况下数据只缓存一天。如果需要，可以设置生存时间(TTL)。如果失去连接，用户仍然能够读取缓存的文档，并使用缓存的更改写入数据，直到连接恢复。需要注意的一件重要事情是，冲突是使用“最后写入获胜”来处理的。

### 图式

如果我使用应用中心数据 SDK，那么我可以使用普通的 CLR 对象，唯一的要求是我有一个 Guid 类型的 ID 属性。

```
public Guid Id { get; set; } = Guid.NewGuid(); 
```

当对象实际保存到 App Center 时，您会看到模式与您预期的略有不同。这是因为 App Centner 添加了一些额外的属性，并将您的模式嵌套到一个名为“document”的对象中。

如果要使用其他服务与数据进行交互，记住这一点非常重要。

[![Screenshot 2019-06-10 at 10.20.28](img/d57a38e0438d641474c2686597e427f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JuDA5V2Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/mikecodes.net/wp-content/uploads/2019/06/Screenshot-2019-06-10-at-10.20.28.png%3Fw%3D750%26ssl%3D1)

## 包装完毕

我刚刚开始探索我可以用 App Center 构建什么，以及如何最好地将其集成到我的应用程序开发流程中。

经过不到一周的开发，我已经开发了一个支持推送通知、数据离线同步和身份验证的应用程序。这种生产力对于我增加功能的能力来说是一个巨大的胜利，因为我专注于应用程序，让应用程序中心团队专注于后端基础设施。

如果你曾经使用过 Parse、Firebase 或 Azure 移动应用，那么我强烈建议你试试 App Center。
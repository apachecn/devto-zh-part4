# 100 行代码的全球分布式无服务器应用程序。包括基础设施！

> 原文：<https://dev.to/mikhailshilkov/globally-distributed-serverless-application-in-100-lines-of-code-infrastructure-included-24i9>

Pulumi 非常擅长将多个云组件连接成一个内聚的应用程序。在我的[上一篇文章](https://blog.pulumi.com/serverless-as-simple-callbacks-with-pulumi-and-azure-functions)中，我介绍了将 JavaScript 或 TypeScript 无服务器功能直接混合到云基础设施程序中的方法。

今天，我将构建一个无服务器应用程序，它的数据存储和 HTTP 端点都靠近最终用户，以确保快速响应。整个应用程序运行在托管的 Azure 服务之上，并在 TypeScript 中定义为单个 Pulumi 程序。

## 基线

我将构建一个 URL shortener:一个简单的 HTTP 端点，它接受 URL 中的短代码，然后将用户重定向到与给定短代码相关联的完整 URL。

我从简单开始，首先制作应用程序的非分布式版本。它由两个主要组件组成:一个用于存储 URL 映射的 Cosmos DB 容器和两个用于处理 HTTP 请求的 Azure 函数:

[![The URL Shortener app deployed to a single location](img/2b3519f88a7b9a5cea9667eddca605a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---rhjR_26--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mikhail.io/2019/07/globally-distributed-serverless-application-in-100-lines-of-code/pulumi-azure-url-shortener-basic.png)

<figcaption>The URL Shortener app deployed to a single location</figcaption>

让我们在一个 Pulumi 程序中定义这个基础设施。

### 宇宙 DB 集合

可以说，在这样一个简单的场景中，Cosmos DB 是多余的:我们只需要一个键值存储，所以表存储就足够了。然而，Cosmos DB 在以后的多区域设置中很方便。

在标准资源组定义的旁边，我创建了一个 Cosmos DB 帐户，其位置设置为单个区域，一致性级别为:

```
import * as pulumi from "@pulumi/pulumi";
import * as azure from "@pulumi/azure";
import * as cosmos from "@azure/cosmos";

const location = "westus"; // To be changed to multiple configurable locations

let resourceGroup = new azure.core.ResourceGroup("UrlShorterner", {
   location,
});

let cosmosdb = new azure.cosmosdb.Account("UrlStore", {
   resourceGroupName: resourceGroup.name,
   location,
   geoLocations: [{ location, failoverPriority: 0 }],
   offerType: "Standard",
   consistencyPolicy: {
       consistencyLevel: "Session",
       maxIntervalInSeconds: 5,
       maxStalenessPrefix: 100,
   },
}); 
```

Cosmos DB 有一个帐户、数据库和容器的层次结构。不幸的是，Cosmos DB 容器还不能被定义为 Pulumi 资源。作为一种变通方法，我定义了一个助手函数`getContainer`来使用 Cosmos SDK 创建一个数据库和一个容器，参见这里的。

### 功能 App

无服务器 Azure 函数将处理我的应用程序中的 HTTP 层。我使用[无服务器函数作为回调](https://blog.pulumi.com/serverless-as-simple-callbacks-with-pulumi-and-azure-functions)的技术在我的 Pulumi 程序内定义 Azure 函数:

```
const fn = new azure.appservice.HttpEventSubscription("GetUrl", {
   resourceGroup,
   location,
   route: "{key}",
   callback: async (_, request: azure.appservice.HttpRequest) => {
       const endpoint = cosmosdb.endpoint.get();
       const masterKey = cosmosdb.primaryMasterKey.get();

       const container = await getContainer(endpoint, masterKey);

       const key = request.params['key'];
       try {
           const response = await container.item(key.toString()).read();
           return {
               status: 301,
               headers: { "location": response.body.url },
               body: '',
           };
       } catch (e) {
           return { status: 404, body: 'Short URL not found' }
       }
   }
});

export const url = fn.url; 
```

我提供模板`{key}`作为`route`参数，以便函数接受通配符 URL 并提取通配符值作为在`request`对象中可用的`key`参数。然后，我使用收到的密钥查找完整的 URL。URL 作为`301`重定向响应的头返回给客户端。`404`如果请求的文档不存在，则返回 Not Found 在这种情况下，顽皮的 Cosmos SDK 会抛出一个错误。

注意 Cosmos DB 参数`endpoint`和`primaryMasterKey`是如何在函数中直接使用的。不需要手动管理密钥或者将它们显式地放入应用程序设置中:生成的密钥在部署时被注入到代码中。在实践中，一个更好的想法是将密钥存储在 KeyVault 中，并通过应用程序设置来读取它，但我将把这一点留在另一篇文章中。

向数据库添加 URL 的函数看起来与上面的非常相似:

```
const fn = new azure.appservice.HttpEventSubscription("AddUrl", {
   resourceGroup,
   methods: ["POST"],
   callback: async (_, request: azure.appservice.HttpRequest) => {
       const endpoint = cosmosdb.endpoint.get();
       const masterKey = cosmosdb.primaryMasterKey.get();
       const container = await getContainer(endpoint, masterKey, location);

       await container.items.create(request.body);
       return { status: 200, body: 'Short URL saved' };
   }
});
export const addEndpoint = fn.url; 
```

主要区别在于:

*   使用`POST`作为接受的 HTTP 方法
*   在 Cosmos DB 中创建新项目
*   使用`request.body`作为有效载荷进行保存

我可能需要在实际的应用程序中添加一些验证——暂时跳过。

### 试一试

现在，当用户导航到像`https://geturl-xyz.azurewebsites.net/api/URLKEY`这样的地址时，他们会被重定向到与该关键字相关的完整 URL。URL 还不是很短，但是我们可以通过自定义域配置来实现。

据推测，我们希望我们的网址缩写程序能在全世界流行起来。因此，一个关键的方面是确保服务快速响应并实现流畅的用户体验。让我们从世界各地测量我们第一个版本的响应时间:

| 位置 | 响应时间 |
| --- | --- |
| 旧金山 | 133 毫秒 |
| 纽约 | 272 毫秒 |
| 伦敦 | 383 毫秒 |
| 法兰克福 | 420 毫秒 |
| 特拉维夫 | 470 毫秒 |
| 香港 | 437 毫秒 |
| 布里斯班 | 449 毫秒 |

我们离美国西部越远，反应就越慢。

我们怎样才能做得更好？

## 让计算和数据更贴近用户

很难超越光速，所以如果我们想快速响应，我们需要将代码和数据带到任何目标用户附近。

幸运的是，其中最复杂的方面——数据分发——可以由 Cosmos DB 多区域帐户来处理。我们需要注意在多个位置部署代码，并将流量路由到最近的位置。计划是这样的:

[![The multi-region URL Shortener app](img/01d02975db1ba3321ac085049de6e33e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wcyw_IVx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://mikhail.io/2019/07/globally-distributed-serverless-application-in-100-lines-of-code/pulumi-azure-url-shortener-distributed.png)

<figcaption>The multi-region URL Shortener app</figcaption>

我并不太在意 Add URL 函数的延迟，所以我没有把它放在图片中。

### 配置位置

上图显示了三个 Azure 区域，但是因为我使用的是通用编程语言，所以我可以用同样的方式处理任意数量的位置。实际上，我将目标区域列表放在 Pulumi 配置文件中，并在执行时读取它:

```
const config = new pulumi.Config();
const locations = config.require("locations").split(',');
const primaryLocation = locations[0]; 
```

区域的顺序定义了优先级。

### 多地区 Cosmos DB 账户

由于`locations`是一个简单的数组，我可以`map`这个数组来产生要为我的 Cosmos DB 设置的`geoLocations`的数组:

```
let cosmosdb = new azure.cosmosdb.Account("url-cosmos", {
   location: primaryLocation,
   geoLocations: locations.map((location, failoverPriority) => ({
       location,
       failoverPriority
   })),
   /* ... other properties stay the same ... */ 
```

就在这里，编程语言的全部力量唾手可得！

### 多区域无服务器 App

> Azure Traffic Manager 是一个基于 DNS 的流量负载平衡器，使您能够在提供高可用性和响应能力的同时，跨全球 Azure 区域将流量最优地分配给服务。

听起来像是我的全球应用程序所需要的！让我们定义一个流量经理配置文件:

```
const profile = new azure.trafficmanager.Profile("UrlShortEndpoint", {
   resourceGroupName: resourceGroup.name,
   trafficRoutingMethod: 'Performance',
   dnsConfigs: [{
       relativeName: "shorturls",
       ttl: 60,
   }],
   monitorConfigs: [{
       protocol: 'HTTP',
       port: 80,
       path: '/api/ping',
   }]
}); 
```

我将路由方法设置为 Performance:

> 当您的端点位于不同的地理位置，并且您希望终端用户使用“最近”的端点以实现最低的网络延迟时，请选择性能。

现在，我需要在每个目标区域创建一个功能应用程序。这很容易通过一个`for..of`循环实现:

```
for (const location of locations) {
   const fn = new azure.appservice.HttpEventSubscription(`GetUrl-${location}`, {
       resourceGroup,
       location,
       route: "{key}",
       callback: /* ... the function stays the same ... */
   });
} 
```

最后，我为每个功能 App 设置了一个端点，将它们绑定到流量管理器。我在与上面相同的循环中这样做:

```
for (const location of locations) {
   const fn = new azure.appservice.HttpEventSubscription(`GetUrl-${location}`, {
       /* ... function definition as shown above ... */
   });

   const app = fn.functionApp;

   new azure.trafficmanager.Endpoint(`tme${location}`, {
       resourceGroupName: resourceGroup.name,
       profileName: profile.name,
       type: 'azureEndpoints',
       targetResourceId: app.id,
       target: app.defaultHostname,
       endpointLocation: app.location,
   });
} 
```

我用一个简单的对象初始化表达式将每个函数 App 的属性分配给相应的端点。

一切就绪。我可以导出流量管理器端点:

```
export const endpoint = pulumi.interpolate `http://${profile.fqdn}/api/{key}`; 
```

是时候重新运行延迟测试了。

## 结果

我在五个 Azure 地区部署了分布式基础设施:美国西部、美国东部、西欧、东亚和澳大利亚东部。结果如下表所示，紧挨着初次运行的值:

| 位置 | 单一区域 | 多个区域 |
| --- | --- | --- |
| 旧金山 | 133 毫秒 | 140 毫秒 |
| 纽约 | 272 毫秒 | 152 毫秒 |
| 伦敦 | 383 毫秒 | 150 毫秒 |
| 法兰克福 | 420 毫秒 | 130 毫秒 |
| 特拉维夫 | 470 毫秒 | 307 毫秒 |
| 香港 | 437 毫秒 | 149 毫秒 |
| 布里斯班 | 449 毫秒 | 529 毫秒 |

我们得到了更平滑的响应时间分布。没有足够靠近特拉维夫的地区，所以它的延迟仍然很高。我检查了布里斯班，流量不知何故被定向到美国东部地区，所以延迟根本没有改善。今天，我想我会坚持“澳大利亚互联网慢”的假设，并建议总是测试你的目标场景，而不要盲目相信供应商。

更重要的是，我向您展示了使用 Pulumi 开发端到端应用程序的场景。我强调了利用来自 TypeScript 领域的熟悉技术的力量。你可以在 [Pulumi 示例](https://github.com/pulumi/examples/tree/master/azure-ts-serverless-url-shortener-global)中找到完整的代码。

云给开发者带来了超能力。你只需要有效地利用它们。
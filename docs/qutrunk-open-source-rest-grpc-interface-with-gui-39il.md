# 带有 GUI 的开源 REST/gRPC 接口

> 原文：<https://dev.to/spinache/qutrunk-open-source-rest-grpc-interface-with-gui-39il>

我刚刚开放了我们的内部项目——qu trunk。它是一个简单的 REST API 接口，用于与队列进行交互。它支持多个后端(目前是 RabbitMQ、MongoDB、Redis)、每个队列的访问令牌、消息日志和统计数据。该应用程序带有可通过 web 浏览器访问的 GUI。
Github:[https://github.com/spinache/qutrunk-api](https://github.com/spinache/qutrunk-api)T3】网站&SaaS app:[https://qutrunk.com](https://qutrunk.com)

特性
5 分钟设置
通过简单的 GET & POST
从 web GUI 管理队列
创建具有第一条消息的队列
多个后端(目前为 MongoDB、RabbitMQ、Redis)
多个接收/消化协议(目前为 HTTP(S)、gRPC)
队列使用统计和添加(消息日志)
访问令牌，具有用于推送/拉取/创建队列的访问控制
StatsD 指标导出
示例使用
您可以简单地推送消息

curl -请求发布\
-URL '[https://app.qutrunk.com/api/v1/core/push/{QUEUE_NAME}?ACCESS _ TOKEN = ACCESS _ TOKEN](https://app.qutrunk.com/api/v1/core/push/%7BQUEUE_NAME%7D?access_token=ACCESS_TOKEN)' \
-data‘hello world’
，用另一个拉动它

curl-request GET-URL '[https://app.qutrunk.com/api/v1/core/pull/{QUEUE_NAME}?ACCESS _ TOKEN = { ACCESS _ TOKEN }](https://app.qutrunk.com/api/v1/core/pull/%7BQUEUE_NAME%7D?access_token=%7BACCESS_TOKEN%7D)'
更多例子在 Golang，Node.js，Java，PHP 在 docs

支持的消息摄取/摘要协议
HTTP(S)
gRPC
即将推出:

Websockets
AMQP
错过了你最喜欢的协议？提交一个问题让我们知道。

支撑的后端〔t0〕mongob〔t1〕rabbitq〔T2〕再〔T3〕很快:

PostgreSQL
MariaDB
没有你喜欢的后端？提交一个问题，让我们知道或编写您自己的集成，并创建一个拉式请求。

安装和配置
要安装 Qutrunk，只需简单地克隆存储库并安装所有必需的依赖项:

git 克隆[git@github.com](mailto:git@github.com):spinache/qutrunk-API . git
CD qutrunk-API；
npm 安装；
CP config . js . dist config . js；
有关详细配置，请参考文档

运行
我们建议至少启动两个监听 HTTP 请求的 Node.js 进程。您可以在那些也将处理 SSL 握手的进程前面设置一个类似 Nginx 的反向代理。有关更多详细信息，请参考文档。

PM2 start-name = app _ 3001 app . js--port = 3001
PM2 start-name = app _ 3002 app . js--port = 3002
托管版
可以用我们的托管版 Qutrunk:[https://qutrunk.com/](https://qutrunk.com/)

它有每月 25 000 条消息的小配额，但它适合任何宠物/小项目。

如果您需要更高的配额，请联系我们:[contact@qutrunk.com](mailto:contact@qutrunk.com)

Qutrunk 是作为一个内部工具创建的，用来集成数十个需要排队的内部服务。我们需要跟踪所有队列的使用和访问，并使用 HTTP 与队列进行交互。另一个原因是物联网，我们使用 Qutrunk 作为一个队列提供者，我们可以在 Raspberry、ESP8266(称为 NodeMCU)和其他小型开发板上使用 HTTP 接口。

一篇关于在物联网中使用 Qutrunk 的博文，并附有 C 语言示例代码

毕竟，我们没有发现任何类似的工具可以使用从 GUI 管理的多个后端。当然，Qutrunk 并不意味着成为 Kafka 或 RabbitMQ 等项目的竞争对手，它更像是一个覆盖接口，为队列提供额外的功能。

目前，我们在 3 节点集群(每个集群有 1 个 vCPU、2gb RAM、20 GB HDD)上部署了一个 MongoDB 副本和 RabbitMQ 副本。它每天处理超过 1 50 万条消息(大约 17 条消息/秒),为我们的其他几个内部项目提供队列服务。

联系人:[contact@quturnk.com](mailto:contact@quturnk.com)

这个项目由 Code Fibers 创建和维护
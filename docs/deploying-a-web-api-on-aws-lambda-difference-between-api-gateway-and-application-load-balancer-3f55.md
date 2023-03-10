# 在 AWS Lambda 上部署 Web API:API 网关和应用程序负载平衡器之间的区别

> 原文：<https://dev.to/unhurried/deploying-a-web-api-on-aws-lambda-difference-between-api-gateway-and-application-load-balancer-3f55>

当我们利用无服务器架构的优势在 AWS Lambda 上部署 web API 时，我们有两个用于 Lambda 前端服务的选项:API 网关(API GW)或应用负载平衡器(ALB)。本文研究了它们之间的区别，以帮助您决定选择哪一个。

##### API 网关独有的特性

*   这是显而易见的，但 API 网关的特定功能，如[请求验证](https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/api-gateway-method-request-validation.html)、[数据映射](https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/mappings.html)、[访问控制](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-control-access-to-api.html)和 [SDK 生成](https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/how-to-generate-sdk.html)不能与 ALB 一起使用。

##### 协议和端口号

*   API GW 仅支持 443 端口上的 HTTPS (TLS 1.2)。注意，尽管 API GW 支持 [VPC 端点](https://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/apigateway-private-apis.html)，但是在这种情况下，您仍然需要使用 HTTPS。
*   ALB 支持任意端口号上的 HTTP 和 HTTPS，TLS 版本也可以从各种[选项](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/create-https-listener.html#describe-ssl-policies)中选择。

##### 请求超时

*   API GW 有一个严格的限制 [29 秒](https://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html#api-gateway-limits)。
*   ALB 本身没有限制，因此应用了λ的超时(最长 15 分钟)。

##### 请求和响应大小

*   API GW 可以处理高达 6MB 的有效载荷，这是 Lambda 有效载荷大小的上限。

    *   注意，6MB 包括头和 Base64 编码的主体，因为有效负载是 Lambda 的请求和响应。
    *   API GW 的有效载荷大小限制为 [10MB](https://docs.aws.amazon.com/apigateway/latest/developerguide/limits.html#api-gateway-limits) 。
*   使用 ALB，请求和响应正文的大小不能超过 1MB。当请求体超过限制时，返回 413 错误，响应体返回 [502 错误](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-troubleshooting.html#http-502-issues)。

##### 请求事件([引用](https://serverless-training.com/articles/api-gateway-vs-application-load-balancer-technical-details/#application-load-balancer-request-event-format-differences))

*   evt.headers(请求)
    *   API GW 设置头名称和值，保持原来的大写和小写，而 ALB 对头名称不大写。
*   evt.queryStringParameters(请求)
    *   没有查询字符串参数时，API GW 设置`null`，而 ALB 设置`{}`。
    *   API Gateway 为查询字符串参数 key 和 value 设置 URL 解码值，而 ALB 设置它们时不进行 URL 解码。
*   evt . multivalueheaders/evt . multivaluequerystringparameters(请求)
    *   API Gateway 默认设置这些参数，而 ALB 要求在配置中启用这些参数。

##### 响应事件([参考](https://serverless-training.com/articles/api-gateway-vs-application-load-balancer-technical-details/#application-load-balancer-response-event-format-differences))

*   ALB 响应事件必须包括 statusDescription、isBaseEncoded 和 headers(当 ALB 配置中启用了“多值头”时)。

##### 成本

*   在一定的请求频率下，API GW 比 ALB 便宜，因为 ALB 是按时间收费的。然而，对于接收大量请求的服务，ALB 更合理。([参考](https://serverless-training.com/articles/save-money-by-replacing-api-gateway-with-application-load-balancer/)

#### 其他引用

*   [亚马逊网络服务博客](https://aws.amazon.com/jp/blogs/networking-and-content-delivery/lambda-functions-as-targets-for-application-load-balancers/)
    *   仅提及如下:`there’s also a lot of overlap and commonality between the parameters used by Amazon API Gateway for triggering Lambda functions and ALB’s parameters.`
*   [API 网关与应用负载平衡器—技术细节](https://serverless-training.com/articles/api-gateway-vs-application-load-balancer-technical-details/)
*   [API Gateway vs. ALB -持田系统](http://www.motidasystem.com/tech/aws/201901271852.html)
    *   它涵盖了请求超时和响应大小限制的区别。
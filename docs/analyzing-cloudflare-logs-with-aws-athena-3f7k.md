# 使用 AWS Athena 分析 Cloudflare 日志

> 原文：<https://dev.to/duffn/analyzing-cloudflare-logs-with-aws-athena-3f7k>

与 Cloudflare 的许多功能一样，您可以通过点击一个按钮来启用他们的[日志推送服务](https://developers.cloudflare.com/logs/logpush/)。Logpush 每隔 5 分钟向您的云存储提供商发送一次您的 HTTP 请求日志。

如果您使用 AWS S3 进行存储，那么您可以利用 [Athena](https://aws.amazon.com/athena/) 来分析您的日志。

> Athena 是一个交互式查询服务，它使得使用标准 SQL 分析亚马逊 S3 中的数据变得容易。

因此，通过一点设置和一些简单的 SQL，您可以分析您的 Cloudflare 日志。

## 表 DDL

然而，为了进行查询，您需要[在 Athena 中创建一个外部表](https://docs.aws.amazon.com/athena/latest/ug/create-table.html),该表与您的 Cloudflare 日志的格式相匹配，这些日志是 JSON 格式的，每条记录都有一个新行。

幸运的是，这在 Athena 中很容易设置。下面是 Cloudflare Logpush 中当前包含的所有字段的 DDL。

*注意:*您可以定制 Logpush 包含的[字段](https://developers.cloudflare.com/logs/logpull-api/requesting-logs/#fields)，因此如果您有，您的字段列表可能与下面的不完全匹配。

```
CREATE EXTERNAL TABLE cloudflare_logs (
    CacheCacheStatus string,
    CacheResponseBytes int,
    CacheResponseStatus int,
    CacheTieredFill boolean,
    ClientASN int,
    ClientCountry string,
    ClientDeviceType string,
    ClientIP string,
    ClientIPClass string,
    ClientRequestBytes int,
    ClientRequestHost string,
    ClientRequestMethod string,
    ClientRequestPath string,
    ClientRequestProtocol string,
    ClientRequestReferer string,
    ClientRequestURI string,
    ClientRequestUserAgent string,
    ClientSSLCipher string,
    ClientSSLProtocol string,
    ClientSrcPort int,
    EdgeColoID int,
    EdgeEndTimestamp string,
    EdgePathingOp string,
    EdgePathingSrc string,
    EdgePathingStatus string,
    EdgeRateLimitAction string,
    EdgeRateLimitID int,
    EdgeRequestHost string,
    EdgeResponseBytes int,
    EdgeResponseCompressionRatio double,
    EdgeResponseContentType string,
    EdgeResponseStatus int,
    EdgeServerIP string,
    EdgeStartTimestamp string,
    FirewallMatchesActions ARRAY < string >,
    FirewallMatchesSources ARRAY < string >,
    FirewallMatchesRuleIDs ARRAY < string >,
    OriginIP string,
    OriginResponseBytes int,
    OriginResponseHTTPExpires string,
    OriginResponseHTTPLastModified string,
    OriginResponseStatus int,
    OriginResponseTime bigint,
    OriginSSLProtocol string,
    ParentRayID string,
    RayID string,
    SecurityLevel string,
    WAFAction string,
    WAFFlags string,
    WAFMatchedVar string,
    WAFProfile string,
    WAFRuleID string,
    WAFRuleMessage string,
    WorkerCPUTime int,
    WorkerStatus string,
    WorkerSubrequest boolean,
    WorkerSubrequestCount int,
    ZoneID bigint)
ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe' LOCATION 's3://my-cloudflare-logs/' 
```

Enter fullscreen mode Exit fullscreen mode

当然，将`s3://my-cloudflare-logs/`改为您在设置 Logpush 时使用的 bucket 的名称。

## 查询

现在我们已经在 Athena 中创建了一个表，我们可以用多种方式分析我们的日志。

通过请求协议检查一下你收到了多少请求怎么样？

```
SELECT count(*) as requests,
         c.clientrequestprotocol
FROM "cloudflare_logs"."cloudflare_logs" c
GROUP BY  c.clientrequestprotocol
ORDER BY  count(*) DESC limit 10; 
```

Enter fullscreen mode Exit fullscreen mode

```
 requests    clientrequestprotocol
1   15063737    HTTP/2
2   6842951     HTTP/1.1
3   4342        HTTP/1.0 
```

Enter fullscreen mode Exit fullscreen mode

或者出于未知的原因，您想要查看今天的平均客户端请求大小(以字节为单位),按 Cloudflare edge colo ID 分组。

```
SELECT avg(clientrequestbytes),
         edgecoloid
FROM "cloudflare_logs"."cloudflare_logs"
-- Assuming you are using the default date format with Logpush
WHERE date_trunc('day', from_iso8601_timestamp(edgestarttimestamp)) = current_date
GROUP BY  edgecoloid
ORDER BY  avg(clientrequestbytes) ASC; 
```

Enter fullscreen mode Exit fullscreen mode

可以想象，分割 Cloudflare HTTP 日志的方式几乎是无限的。享受在 Cloudflare 日志上潜水的乐趣吧！
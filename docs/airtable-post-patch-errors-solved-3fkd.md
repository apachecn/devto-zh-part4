# Airtable 开机自检/补丁错误已解决

> 原文：<https://dev.to/markokolombo/airtable-post-patch-errors-solved-3fkd>

经过一个令人沮丧的晚上，我终于发现了为什么 Airtable API 总是拒绝我卑微的 POST/PATCH 请求，也就是需要参数作为主体发送的请求。

我被一些错误卡住了:

> { " type ":" INVALID _ REQUEST _ MISSING _ FIELDS "，" message ":"在请求正文" }}中找不到字段" FIELDS "，"
> 
> { " error ":{ " type ":" INVALID _ REQUEST _ UNKNOWN "，" message ":"无效请求:参数验证失败。请检查您的请求数据。}}
> 
> { " error ":{ " type ":" INVALID _ REQUEST _ BODY "，" message ":"无法解析请求正文" }}

解决方案是...只是把这个加到 json: **"typecast:true"** 。
是的，其他一切都很好。

来自 Airtable 的 API 文档:
"如果传入了 typecast 参数，Airtable API 将尽最大努力从字符串值自动转换数据(单击以显示示例)。默认情况下，自动转换是禁用的，以确保数据完整性，但它可能有助于与第三方数据源集成。

老实说，这是有点隐藏，但嘿，我写这篇文章是专门为你，争取胜利(阅读:为自己当我会忘记)。

json 示例

``javascript
{
【字段】:{
【客户】:[
"recMmE0pTasdGqkEw"
]，
"日期": " 2019-07-15 "，
"自":" 2019-06-15 "，
"至":" 2019-06-30 "，
"货币":"美元"
}，【日期】。`

 `}
`

 `编辑:
我刚刚发现，在我用 **typecast:true** 发送请求后，现在所有的请求都顺利通过，没有一个错误！``
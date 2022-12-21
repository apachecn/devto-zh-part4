# 有效处理日期和时区的 3 个简单规则

> 原文：<https://dev.to/corykeane/3-simple-rules-for-effectively-handling-dates-and-timezones-1pe0>

当被问及是否为处理日期和时区而苦恼时，超过 50%的开发人员回答“是”。我的猜测是，对这项调查说“不”的人要么已经想好了，要么他们没有使用必须迎合全球用户的平台/应用程序。

总之，我想写一篇博文，详细介绍我是如何处理日期和时区的。我认为遵循这些简单的规则，你就不会那么头疼了。

规则#1 -在你的数据库中存储 UTC 格式的日期时间，以及后端代码。保持所有日期相关数据的一致性非常重要。在数据库中存储日期时，它们应该始终使用 UTC。如果您不熟悉 UTC 是什么，它是所有主要时区都基于的主要时间标准。主要时区只是相对于 UTC 的偏移量。此外，当您用后端代码处理 datetime 时，请确保它是 UTC 格式的。

规则#2 -使用前端代码将日期时间转换为用户的本地时区。虽然你的后端会返回 UTC 时间，但是前端可以很容易地将这些时间转换成用户的本地时区。这样做在后端(以 UTC 表示的句柄)和前端(以用户的本地时间表示的句柄)之间建立了职责分离。通过使用一个标准，比如 ISO 8601，保持你的日期时间在前端的格式一致。当您向后端发送请求时，请以 ISO 8601 格式发送日期时间，以便后端可以轻松地将其转换为相应的 UTC 日期时间。

规则 3——使用日期时间库。为了更好地处理日期时间，所有主要的 web 开发语言/框架中都有库。这使得基于标准(例如 ISO 8601)进行转换或格式化变得容易得多。JavaScript 中的一个示例库叫做 moment.js，PHP 中的一个示例库叫做 Carbon。

通过遵循这些规则，你应该在如何处理约会时间方面灌输一个有效的过程。

资源:

*   UTC 时间标准-[https://en.wikipedia.org/wiki/Coordinated_Universal_Time](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)
*   https://en.wikipedia.org/wiki/ISO_8601 时间格式标准-[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)
*   时刻 JS 日期时间库-[https://momentjs.com/](https://momentjs.com/)
*   碳 PHP 日期时间库-[https://carbon.nesbot.com/docs/](https://carbon.nesbot.com/docs/)
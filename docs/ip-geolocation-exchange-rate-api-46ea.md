# IP 地理位置和汇率 API

> 原文：<https://dev.to/enderk/ip-geolocation-exchange-rate-api-46ea>

这项服务是免费的。Maxmind-DB 用于地理位置确定。欧洲中央银行和土耳其共和国中央银行过去经常交换汇率。

**开发**
使用 Swoole Http Server 编写。它在一台 4 核机器上实现了平均“35k/秒”的请求。

**PHP 库**

*   Swoole
*   快速路线
*   Maxmind 阅读器

**自动更新**
Swool 调度器是用连续更新制成的。每 24 小时检查一次更新。数据库一个月下载 2 次。

[转到 Github 项目](https://github.com/appaydin/gclib)
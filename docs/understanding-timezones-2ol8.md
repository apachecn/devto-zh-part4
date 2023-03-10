# 了解时区

> 原文：<https://dev.to/elasticrash/understanding-timezones-2ol8>

最近，我被指派在一个 API 中创建一个过滤器，只给你特定时间范围内发生在上午或下午的事件(通过传递一个时区)。幸运的是，在我们的数据库中，除了纪元时间之外，我们还有一个日期类型字段和一个时间类型字段。

时间类型是特殊的，因为它被设计为不仅专门存储一天中的时间，而且还存储两个事件之间经过的时间。

在后一种情况下，时间可以取-838:59:59 到 838:59:59 之间的值。

从 23:00:00 到 01:00:00 进行搜索也是无效的，而是需要从
23:00:00 到 23:59:59 和
00:00:00 到 01:00:00 进行搜索

因此，根据我们必须投影的时区，将时区 AM 和 PM 转换为 UTC，并相应地进行分割。

所以我和一个同事一起制作了下图，这让事情变得非常简单，我觉得分享它是个不错的主意。

[![](img/c0173164a0f07af0c1a1bb6281cf5d63.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wJrbufNb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5j2vcg5rezj7sny8bw10.png)
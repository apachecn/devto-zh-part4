# 如何在 MySQL 数据库表中生成随机 id，这些 id 不是单个唯一的，而是跨两个字段(列)唯一的？

> 原文：<https://dev.to/kp/how-to-generate-random-ids-that-are-not-individually-unique-but-unique-across-2-fields-columns-in-a-mysql-database-table-2dbd>

关于如何在 MySQL 中生成唯一的、随机的字母数字字符串，已经有了答案。这不是问题所在。

我试图在 MySQL 中生成一个 4 字符的随机字母数字字符串，这样该字段和另一个字段的组合在 MySQL 中是唯一的。换句话说，给定表中的一个现有字段(userID)，我需要一个 4 个字符的随机字母数字字符串用于同一个表中的另一个字段(commentUID)，这样 userID+commentUID 的组合是惟一的。请注意，commentUID 本身不一定是唯一的——两个或更多具有不同 userIDs 的用户可以具有相同的 commentUID 值。

背景:我有一个用户表和一个评论表。我需要一个唯一的组合:用户的用户名(或 ID)和他们留下的评论的唯一 ID，以便系统中的每个评论都可以在 URL 中唯一地标识，同时还保持评论 uid(因此 URL)尽可能短。

评论的唯一 ID 应该是 4 个字母数字字符，因为每个用户不太可能留下超过 36^4 = 1.6M 的评论(36 表示 a-z0-9 中所有可能的值，仅小写)。这方面的一个例子实际上是 dev.to。参见[https://dev.to/mindplay/comment/dm5i](https://dev.to/mindplay/comment/dm5i)

作为一个纯 MySQL 的解决方案，我如何才能做到这一点，以便在 comments 表中插入新记录时生成 commentID？如果你知道如何做到这一点，你的想法是值得赞赏的。
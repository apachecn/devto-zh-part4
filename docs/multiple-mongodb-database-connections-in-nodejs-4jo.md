# NodeJS 中的多个 MongoDB 数据库连接

> 原文：<https://dev.to/abhi11210646/multiple-mongodb-database-connections-in-nodejs-4jo>

MongoDB 允许配置一个隐藏的辅助副本节点。这意味着在主节点出现故障的情况下，辅助副本节点不会被选为主节点。等等…但是我们为什么需要这样一个复制节点呢？答案是，应用程序可以将其分析(大量读取)查询转移到该节点，这样主节点就不会因为大量读取分析查询而出现任何问题。
阅读更多...

[https://medium . com/@ abhi 11210646/multiple-MongoDB-database-connections-in-nodejs-ee 4b 75 cc 70 e 3](https://medium.com/@abhi11210646/multiple-mongodb-database-connections-in-nodejs-ee4b75cc70e3)
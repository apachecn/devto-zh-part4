# 从 node.js 连接到 oracledb

> 原文：<https://dev.to/thehanna/connect-to-oracledb-from-nodejs-37om>

我目前使用 Java ADF REST web 服务来访问大量遗留 Oracle 数据库中的数据。它是缓慢的、静态的、一维的，是一个巨大的痛点。我将 NodeJS 用于应用服务器，所以我希望实现一些更干净、更动态的 API 来与 Oracle 数据库接口。

然而，我无论如何也想不出如何从 NodeJS 连接到这个数据库。我可以通过 SQL Developer 访问数据库。我在集成的 WebLogic Server 中设置了一个数据源，它随 JDeveloper 一起提供给当前的 web 服务，这些服务(看起来)使用所有相同的信息。我有所有的信息:主机名、用户名、密码、端口和 SID。我已经在 Mac 上安装了 InstantClient 并添加到了我的路径中。在 NodeJS 中，该信息的任何配置似乎都无法正常工作。我在 NodeJS v10.15.3 上使用的是 [node-oracledb](https://www.npmjs.com/package/oracledb) 版本 3.1.2

```
{
  user: 'my_username',
  password: 'my_password',
  connectString: 'app.company.com:port:database_name'
} 
```

上述配置会导致错误:

`Error: ORA-12545: Connect failed because target host or object does not exist`

所以很明显我的连接字符串有问题。我还在`connectString`上尝试了多种排列方式，包括:

*   `app.company.com:port/database_name`
    *   `Error: ORA-12514: TNS:listener does not currently know of service requested in connect descriptor`
*   `jdbc:oracle:thin:@app.company.com:port:database_name`
    *   使用 SQL Developer 和 WebLogic Server 数据源，但在 NodeJS 中使用时会产生错误`ORA-12514`

我在抓救命稻草。我发现的所有信息都过于密集，基本上毫无用处。我不是 Java 开发人员。我以前从未大量使用过 Oracle 数据库。我确信我遗漏了一些奇怪的、神秘的配置*东西*，但是我甚至不知道该问什么问题。实际上*任何额外的信息都会非常有用。*
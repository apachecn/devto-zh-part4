# 在 AWS Lambda 中管理数据库负载的 6 个关键

> 原文：<https://dev.to/byrro/6-keys-to-managing-db-load-in-aws-lambda-99k>

数据库连接有时会被忽略，甚至出错。对于无服务器开发人员，我们只希望有一种像 Lambda 本身一样简单的方法来管理数据库连接。

# 先做第一件事

由于无服务器架构与我们习惯的架构有很大不同，因此有一些重要的考虑事项需要考虑。

我们首先需要考虑的是可伸缩性。默认情况下，AWS Lambda 可以在几毫秒内扩展到 1，000 个并发请求。而且有可能通过问 AWS 来解除这个限制。例如，这对数据库连接管理有重要影响。

# 拉伸或堵塞

我们的数据库基础设施能够满足这种需求吗？如果 DB 成为瓶颈会发生什么，我们的应用程序会如何表现？它会丢失数据吗，或者它的逻辑中有某种“应急计划”编码吗？在数据库出现故障的情况下，它是否能很好地响应最终用户的请求？

[![Stretch](img/1573a2a0df7a20cba741900b125ea039.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pfRZHKCX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tv2twvlrcx7lymii4279.PNG)

可以把 Lambda 想象成一条宽阔的高速公路，能够容纳大量的交通流量，从轿车到重型卡车再到公共汽车。我们不应该把所有的交通改道到一条狭窄的街道上，这会造成严重的交通堵塞。

它可以糟糕到陷入僵局，没有人能回家，就像下面的十字路口。

[![Traffic gridlock](img/abf32cb7a02f2202acb896caf0515417.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zJEXBkJe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7m3xx3cv39jrly5dy4ig.jpg)

这可能发生在数据库错误触发[λ自动重试](https://docs.aws.amazon.com/lambda/latest/dg/retries-on-errors.html)的情况下，这可能会以一种糟糕的方式升级。

了解我们的数据库能够处理多少负载并密切监控 Lambda 是很重要的。 [AWS CloudWatch 指标](https://docs.aws.amazon.com/lambda/latest/dg/monitoring-functions-metrics.html)非常有用。对于在生产中运行的专业应用程序，第三方监控服务可以方便地获得更深入的指标和严格的控制，如 [Dashbird](https://dashbird.io/) 、 [DataDog](https://www.datadoghq.com/) 和 [New Relic](https://newrelic.com/) 。

# 一刀切……只有一个！

这里没有放之四海而皆准的方法。我们实现 Lambda 和数据库之间的可伸缩性协调的方式将取决于应用程序和存储系统。

[![Tools sizes](img/4de33c19cba1b20430799ddabb3cc423.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z5FOV35Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rgksulmbwoq8n9ookf4q.PNG)

考虑一个应用程序，有大量用户偶尔生成小块数据，少数大客户急切地每秒生成大量数据。

我们为分布式读/写请求和无缝水平扩展设置了数据库基础架构。又好又甜。

数据库可能被分割在多个服务器上(称之为分区)，但是我们可能无法将负载均匀地分布在所有服务器上。假设我们基于客户跨分区分配负载。客户 1 到 10 将在分区 1 中结束，客户 11 到 20 在分区 2 中结束，以此类推(为了便于说明，过于简化)。

当一个大客户生成要处理的数据时，所有的东西都会集中到一个分区中，这可能会负担过重。

[![Huge rock balance](img/804e072ff3b5d6e258296df2e83ee4fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YjP3B7kE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hxdm7d181pxh9ai76a9g.PNG)

在设计我们的处理和存储逻辑时，我们必须考虑到每个客户的数据分布是偏斜的。最简单的方法是提前为每个客户分配容量。

例如，如果给定的客户表明其每秒需要 X TB 的数据，我们可以提前计算出需要多少台服务器来满足需求并设置专用分区。

# 永恒不在 DB 连接词汇中

每个数据库服务器在任何一个时间点都有一定的连接数限制。让空闲连接永久开放显然是个坏主意，因为连接槽很少。

在传统的基础设施中，我们有一个或多个服务器，每个服务器处理相当大的一部分负载，我们可以在每个服务器中分配一定数量的连接，这些连接被共享以服务于多个请求者。

由于我们的服务器有很长的使用寿命，并且我们完全控制，我们可以更容易地以一种高性能的方式管理数据库连接。

[![Wire connections](img/c809711fa0192eecf3c2ebbec69cea07.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JrPQyEDX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eyj3hrh4agk8et15mvg2.PNG)

在 Lambda，事情完全不同。我们对容器管理的控制非常有限，更不用说运行它们的底层服务器了。甚至不可能控制两个 Lambda 实例是否运行在同一个服务器上。

如果我们恰当地遵循 Lambda 分布式模型，每个 Lambda 实例将处理一个请求。这意味着:在一个给定的 Lambda 容器中，我们不能有一个数据库连接池在众多请求者之间共享。

有人可能会说:“*我可以在我的 Lambda 函数处理程序之外打开一个连接，并让它为下一次调用*而打开”。

听起来很有趣。我们通过重用连接来减少整体延迟。但这不是个好主意。两个问题:如果我们在 Lambda 函数处理程序之外打开连接，我们如何控制...

*   ...何时以及如何关闭连接？
*   ...打开了多少个连接，它们在哪里被使用？

答案是:我们没有。这不是管理数据库连接的好方法。

[![Alt Text](img/5acf7e782d737c41ccdf419f8c64e330.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sWyRm50R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jturfz9yobk1qdke3nxa.PNG)

Lambda 容器可能会在服务一个调用后保持空闲几分钟，甚至几个小时。当一个调用进来并且一个 DB 连接在处理程序之外被打开时，它将在一段可变的时间内与容器保持打开状态，并且我们不能强制执行一种重用该连接的方法。

Lambda 的最佳实践是打开函数处理程序内部的连接，并在终止调用之前，在工作完成后立即关闭它们。每次调用都打开一个连接会增加延迟，但是没有更好的方法来管理它。

# 超时时不超时

Lambda 的限制之一是执行时间。比如说一个功能限制在 10 秒。您打开了一个 DB 连接，但是由于某种原因，该作业的处理时间比正常情况下要长得多。十秒钟过去了，由于 Lambda 限制，执行暂停，DB 连接可能保持打开。

Lambda 超时不会立即自动超时数据库连接！

[![Timeout](img/b94c53d086db915915112b38d53437d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--em9DXt4L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ssr3qhtnc59xrkrmfqec.png)

现在，考虑问题仍然存在，并且在给定的一段时间内超时频繁发生。数据库服务器可能会很快因多个空闲连接而不堪重负，使得其他 Lambda 调用很难甚至不可能访问它，直到这些连接被关闭。

为了避免这个问题，我们可以使用 Lambda 上下文信息来监控在执行超时之前剩下的毫秒数:

1.  对于每个 Lambda 调用，启动一个单独的线程与我们的代码并发运行；
2.  每隔几秒钟检查一次剩余时间；
3.  当它太接近零时(意味着函数将要超时)，它将在调用停止之前预防性地关闭 DB 连接；

用简化的术语说明概念:

```
import logging
import time
import threading

# Dummy database objects from database import (
    DatabaseConnection,
    DatabaseException,
    db_host,
)
import handler_executor

def handler(event, context):
    try:
        # Open a connection
        db_conn = DatabaseConn(**db_host)

        # Start a background thread to monitor risk of timeout
        timeout_thread = threading.Thread(
            target=monitor_timeout,
            args=(context, db_conn),
            daemon=True
        )

        response = handler_executor(event, context, db_conn)

    except DatabaseException as error:
        logging.exception(error)

        response = {'status': 500, 'error': type(error).__name__}

    finally:
        # Make sure the connection is finally closed, if still open
        if 'db_conn' in locals() and isinstance(db_conn, DatabaseConn) and \
                db_conn.is_open():
            db_conn.close()

        return response

def monitor_timeout(context, db_conn):
    # Check remaining time every two seconds
    while context.get_remaining_time_in_millis() >= 2500:
        time.sleep(2)

    # System will exit the loop if the remaining time < 2500 ms,
    # in which case we close the connection preventively
    if db_conn.is_open():
        db_conn.close() 
```

# 无连接数据库

也许将 Lambda 这样的无服务器计算引擎与基于服务器的存储系统混合在一起并不是最好的方法。很难很好地调整可伸缩性。

据我们所知，现在有很好的无服务器、基于 API 的存储系统。我们不需要管理服务器，也不需要在启动数据查询之前建立连接。例如，API 数据库将通过 HTTP 端点直接接受查询。

[![Road arrow signs](img/085b31e7f32b9168fd51b9701384018d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ihOvvGV2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uk6bh9lswciu3r5qtx3r.PNG)

AWS 在这方面做得很好，有 Graph、SQL 和非 SQL 风格的无服务器数据库。

DynamoDB 是最老的选项:一个非 SQL 的键值存储数据库。过去，它有一些可伸缩性问题，引起了人们的负面关注，但这些都是过去的事情了。目前，人们可以很容易地将 Dynamo 扩展到 40，000 个并发请求，这对大多数 Lambda 应用程序来说应该足够了。通过一些巧妙的数据库架构设计，DynamoDB 可以扩展到虚拟无限。我个人在许多项目中使用了它，并取得了巨大的成功。

对于 SQL 的粉丝来说，Aurora Serverless 现在[通过*数据 API* 支持 API 查询](https://aws.amazon.com/pt/blogs/aws/new-data-api-for-amazon-aurora-serverless/)。不幸的是，在撰写本文时只支持 MySQL，但是 Aurora 团队肯定会在不久的将来发布 Postgres 支持。

在图形空间中，我们有[海王星](https://aws.amazon.com/neptune/)和[云目录](https://aws.amazon.com/pt/cloud-directory/)。图表并不完全是上一个的目的，但它可以用于这个目的。

所有这些基于 API 的数据库都符合 Lambda 模型:它们是完全托管的，可以快速伸缩以适应不断变化的需求，不需要连接开销。
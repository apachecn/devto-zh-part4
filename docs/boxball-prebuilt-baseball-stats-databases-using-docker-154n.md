# Boxball:使用 Docker 预先构建的棒球统计数据库

> 原文：<https://dev.to/droher/boxball-prebuilt-baseball-stats-databases-using-docker-154n>

[Boxball](https://github.com/droher/boxball) 创建两个最重要的开源棒球数据集的预填充数据库: [Retrosheet](http://retrosheet.org) 和[棒球数据库](https://github.com/chadwickbureau/baseballdatabank)。Retrosheet 包含自 2000 年以来的每场大联盟比赛、自 1937 年以来的每场比赛、自 1906 年以来的每场比赛以及自 1871 年以来的每场比赛的信息。数据库(基于拉赫曼数据库)包含了历史上每个球员和球队的年度总结。除了数据和数据库本身，Boxball 还依赖以下工具:

*   [Docker](https://docs.docker.com/engine/docker-overview/) 可重复构建，易于分发
*   [SQLAlchemy](https://www.sqlalchemy.org/) 用于抽象出数据库之间的 DDL 差异
*   [Chadwick](https://github.com/chadwickbureau/chadwick) 用于将 Retrosheet 的复杂事件文件转换成关系格式

按照下面的说明安装您选择的发行版。Docker Hub 上也有全套图片。

代码中广泛记录了追溯表模式；在我找到更好的解决方案之前，请在此处查看来源。

如果您认为该项目有用，请考虑捐赠给:

*   为无家可归的 LGBTQ 青年设立的阿里·福尼中心
*   [350.org](https://act.350.org/donate/build/)，一个草根的国际气候变化组织

如有问题或意见，请随时[联系我](//mailto:david@boxball.io)！

## 要求

*   [Docker](https://docs.docker.com/install/) (v18.06，早期版本可能不工作)
*   2-20GB 磁盘空间(取决于分发选择)
*   Docker 可使用 500MB-8GB 内存(取决于发行版本选择)

## 分布图

### 面向列的数据库

#### CST ore _ fdw 研究生

这个发行版使用了 [cstore_fdw](https://github.com/citusdata/cstore_fdw) 扩展将 PostgreSQL 转换成面向列的数据库。这意味着你得到了 Postgres 丰富的特性集，但是在速度和磁盘使用上有了巨大的提高。要安装和运行数据库服务器，请执行以下操作:

`docker run --name postgres-cstore-fdw -d -p 5433:5432 -v ~/boxball/postgres-cstore-fdw:/var/lib/postgresql/data doublewick/boxball:postgres-cstore-fdw-0.0.2`

大约在映像下载一小时后，数据将被完全加载到数据库中，您可以通过端口`5433`连接到它(使用`psql`命令行工具或您选择的数据库客户端)。数据将以`~/boxball/postgres-cstore-fdw` (~1.5GB)的形式保存在您的机器上，这意味着您可以停止/移除容器，而不必在重新打开时重新加载数据。

#### 点击屋

[Clickhouse](https://clickhouse.yandex/) 是 Yandex 开发的一个数据库，具有一些非常令人印象深刻的性能基准。它使用的磁盘空间比 Postgres cstore_fdw 少，但 RAM 却多得多(~5GB)。我还没有运行任何查询性能比较。要安装和运行数据库服务器，请执行以下操作:

`docker run --name clickhouse -d -p 8123:8123 -v ~/boxball/clickhouse:/var/lib/clickhouse doublewick/boxball:clickhouse-0.0.2`

映像下载 15-30 分钟后，数据将完全加载到数据库中，您可以通过连接容器并使用`clickhouse-client` CLI 或使用端口`8123`上的本地数据库客户端来连接到它。

#### 钻

[Drill](https://drill.apache.org/) 是一个允许直接在文件上进行 SQL 查询的框架，无需声明任何模式。它通常用于具有大量数据集的计算集群，但是我们使用单节点设置。要安装和运行:

`docker run --name drill -id -p 8047:8047 -p 31010:31010 -v ~/boxball/drill:/data doublewick/boxball:drill-0.0.2`

图像下载后，数据将立即可供查询。使用端口`8047`访问 Web UI(包括一个 SQL runner ),使用端口`31010`通过数据库客户端进行连接。
你也可以附加容器并从命令行查询。

### 传统(面向行)数据库

注意:这些框架在查询逐场播放数据时可能会非常慢，并且它们比它们的柱状对应物占用更多的磁盘空间。

#### Postgres

与上面的 cstore_fdw 扩展版本的配置类似，但以传统方式存储。

`docker run --name postgres -d -p 5432:5432 -v ~/boxball/postgres:/var/lib/postgresql/data doublewick/boxball:postgres-0.0.2`

大约在映像下载 90 分钟后，数据将完全加载到数据库中，您可以通过端口`5432`
连接到它(使用`psql`命令行工具或您选择的数据库客户端)。

#### [MySQL](#mysql)

要安装和运行:

`docker run --name mysql -d -p 3306:3306 -v ~/boxball/mysql:/var/lib/mysql doublewick/boxball:mysql-0.0.2`

大约在图像下载两个小时后，数据将被完全加载到数据库中，您可以通过端口`3306`连接到它。

#### SQLite(带 web UI)

要安装和运行:

`docker run --name sqlite -d -p 8080:8080 -v ~/boxball/sqlite:/db doublewick/boxball:sqlite-0.0.2`

图像下载后大约两分钟，数据将完全加载到数据库中。`localhost:8080`将提供一个 [web UI](https://github.com/coleifer/sqlite-web) ，你可以在其中编写查询和执行模式探索。

### 平面文件下载

#### 检察院

Parquet 是一种柱状数据格式，最初是为 Hadoop 生态系统开发的。它在 Spark、Pandas 和许多其他框架中都有坚实的支持。
[OneDrive](https://1drv.ms/u/s!AtpEocFNRNBWg1eR5L-U7bupJqyt?e=RbxuMp)

#### CSV

提取步骤中的原始 CSV(文件夹存储为`.tar.gz`)。
[OneDrive](https://1drv.ms/u/s!AtpEocFNRNBWhAb_gwNbBLPB1pDv?e=qyrU3L)

## 鸣谢

Ted Turocy 的 [Chadwick Bureau](http://chadwick-bureau.com/) 开发了使这个项目成为可能的工具和回购。我也感谢[肖恩·拉赫曼](http://www.seanlahman.com/)创建了他的数据库，我已经用了超过 15 年了。由于 CircleCI、Github 和 Docker Hub 慷慨的开源计划，我能够免费开发和主持这个项目。

Retrosheet 代表了数千名棒球迷在 150 年的记分和数据输入过程中的集体努力。我希望 Boxball 促进更多的历史研究来延续这一传统。
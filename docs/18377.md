# SimQLe——Python 中非常简单的 SQL

> 原文：<https://dev.to/harlekuin/simqle-very-simple-sql-in-python-23h1>

# SimQLe

大家好，

我发现在工作中创建各种需要连接到 SQL 数据库的 Python 项目时，不容易勾选以下所有框:

*   在任何项目中易于读写的 SQL 集成
*   同时管理多个数据库连接(包括多种类型的 SQL 数据库)
*   尽快在快速脚本中获得可靠的 SQL 连接并运行
*   秘密地存储连接字符串、驱动程序等。环境变量

**但最重要的是**:

*   让用测试数据库实例编写集成测试变得非常容易，其中项目的代码不需要改变！此外，通过最佳实践这样做，您可以 100%确定您的项目在测试期间不会触及生产数据库。

SimQLe 的创建就是为了解决所有这些问题。它基于令人惊叹的 SQLAlchemy 库。SimQLe 希望在以下情况下做到完美:

*   你不需要 ORM，只需要 SQL 查询，
*   您想要一种非常简单的方法来管理数据库，并且
*   您想要超级简单、健壮的测试。

## 基础知识

安装它:`$ pip install simqle`

在任何地方创建一个类似于
的`.connections.yaml`文件

```
connections:
  - name: my-sql-server-database
    driver: mssql+pyodbc:///?odbc_connect=
    connection: DRIVER={SQL Server};UID=<username>;PWD=<password>;SERVER=<my-server>
    url_escape: True

test-connections:
    # the names of the test-connections should mirror the connections above.
  - name: my-sql-server-database
    driver: mssql+pyodbc:///?odbc_connect=
    connection: DRIVER={SQL Server};UID=<username>;PWD=<password>;SERVER=<my-test-server>
    url_escape: True 
```

然后写一些 python 这样的:

```
from simqle import recordset, load_connections

load_connections("./.connections.yaml")

sql = "SELECT name, age FROM people WHERE category = :category"
params = {"category": 5}
result = recordset(con_name="my-database", sql=sql, params=params) 
```

然后，当你编写测试时，只需在运行测试之前将`SIMQLE_TEST`环境变量翻转为`True`，整个项目将使用在`test-connections`中定义的连接。注意测试连接中的服务器是如何不同的。瞧啊。无压力测试，实际的项目和以前一样干净。

确保`.connections.yaml`在你的。gitignore 用于协作时的安全连接。

它还可以做更多的事情——但是上面的内容展示了它可以有多简单。它有命名参数，引擎曝光，如果你想把它们用于 pandas 的奇妙的 read_sql，100%的覆盖率，并与每一个主要的开源 sql 数据库进行了全面测试。查看回购了解更多信息。

如果你有任何想法或问题，请告诉我！路线图上有一些升级:)

Github 回购:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)哈克宁 / [辛姆凯](https://github.com/Harlekuin/SimQLe)

### 在 Python 中使用 SQL 的最简单方法

<article class="markdown-body entry-content container-lg" itemprop="text">

# 简单的

> SQL 的简单方法

[![build status](img/ee24c37d889545acf0213c4c5c8bc102.png) ](https://travis-ci.org/Harlekuin/SimQLe) [ ![codecov](img/84a4317f16b524e048745b4676df99ba.png)](https://codecov.io/gh/Harlekuin/SimQLe)

非常适合 Python 项目中的简单 SQL。执行 SQL 并返回带有命名参数的简单记录集。管理多个连接，并在生产、开发和测试模式之间切换。

文档可以在[这里](https://simqle.readthedocs.io/en/latest/)找到

## 装置

### 贮藏室ˌ仓库

[https://github . com/harekin/simqle](https://github.com/Harlekuin/SimQLe)

或者选择你的毒药:

*   `$ pip install simqle`
*   `$ poetry add simqle`
*   `$ pipenv install simqle`

SimQLe 从 yaml 格式的连接文件中读取。更多详情见`.connections.yaml`文件部分。

## 使用

### 生产中

从您的连接名称、SQL 语句和参数字典中获得一个结果:

```
from simqle import ConnectionManager
# Intialise your connections
cm = ConnectionManager(".connections.yaml")
# Write some simple SQL
sql = "SELECT name, age FROM people WHERE category = :category"
params = {"category": 5}
result = cm.recordset(con_name="my-database", sql=sql, params=
```

…</article>

[View on GitHub](https://github.com/Harlekuin/SimQLe)
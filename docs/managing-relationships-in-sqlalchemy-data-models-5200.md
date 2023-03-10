# 管理 SQLAlchemy 数据模型中的关系

> 原文：<https://dev.to/hackersandslackers/managing-relationships-in-sqlalchemy-data-models-5200>

[![Managing Relationships in SQLAlchemy Data Models](img/485e17a82b1c07468844da3ef253ed8e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SJ7oLQP---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/hackersandslackers-cdn/2019/07/sqlalchemy-relationships.jpg)

使用 SQLAlchemy 有很多很好的理由，从[管理数据库连接](https://dev.to/hackersandslackers/pythonic-database-management-with-sqlalchemy-37m3)到与 Pandas 等库的轻松集成。如果你从事应用程序开发业务，我敢打赌，通过 ORM 管理应用程序的数据是 SQLAlchemy 用例列表的第一位。

大多数软件工程师可能会发现数据库模型管理比 SQL 查询更容易。对于拥有大量数据背景的人(比如我们)，增加的抽象可能有点令人不快:为什么我们*需要*外键来执行两个表之间的连接？为什么我们需要区分什么时候表会有一个**一对多**关系，而不是一个**多对多**关系？这些都不是 SQL 的限制，那么所有的“额外工作”是什么呢？

使用 ORM 的目的是通过在我们的应用程序中将数据库概念转化为易于复制的代码，减少人们构建应用程序的工作。今天我们将学习定义 SQLAlchemy 数据模型，特别关注管理表关系。

我们将关注普通的 SQLAlchemy:如果你正在寻找如何在 Flask 中实现 SQLAlchemy 数据模型的指南，那么在你完成这里的工作后，也许值得看看[这篇文章](https://dev.to/hackersandslackers/manage-database-models-with-flask-sqlalchemy-22n8)。<！-kg-卡-end: html - >

## 基本模型定义

在开始定义关系之前，让我们回顾一下如何定义一个基本的数据库模型。

SQLAlchemy 数据库模型是扩展一个 SQLAlchemy 库(比如一个`declarive_base()`)的类，我们从`sqlalchemy`包中导入这个库。数据库模型由**列**组成，这些列是`sqlalchemy`特有的数据类型:

```
from sqlalchemy import Column, Integer, String, Text, DateTime, Float, Boolean, PickleType
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()

class ExampleModel(Base):
    """Data model example."""
    __tablename__ = "example_table"
    __table_args__ = {"schema": "example"}

    id = Column(Integer,
                primary_key=True,
                nullable=False)
    name = Column(String(100),
                  nullable=False)
    description = Column(Text,
                         nullable=True)
    join_date = Column(DateTime,
                       nullable=False)
    vip = Column(Boolean,
                 nullable=False)
    number = Column(Float,
                    nullable=False)
    data = Column(PickleType,
                  nullable=False)

    def __repr__ (self):
        return '<Example model {}>'.format(self.id) 
```

Enter fullscreen mode Exit fullscreen mode

上面创建了一个模型，它利用了通过 **sqlalchemy** 提供给我们的每个列类型。我们创建的每个数据库模型都对应一个表，其中我们的模型中的每个**列**对象都代表结果表中的一列。当我们的应用程序初始化 SQLAlchemy 时，SQLAlchemy 将在我们的数据库中创建表来匹配每个模型(假设还没有模型)。

我们还在模型中设置了几个可选的内置变量。`__tablename__`确定结果数据库表的名称，而`__table_args__`允许我们设置我们的表将位于哪个 Postgres 模式中(如果适用)。

### 从我们的模型创建表格

创建好模型后，我们需要在数据库中显式创建结果表。我们通过在**基础**对象上调用`create_all()`来实现，这个对象是我们的模型扩展的对象。这里有一个简单的脚本可以帮我们解决这个问题:

```
from models import Base
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from os import environ

# Create engine db_uri = environ.get('SQLALCHEMY_DATABASE_URI')
engine = create_engine(db_uri, echo=True)

# Create All Tables Base.metadata.create_all(engine) 
```

Enter fullscreen mode Exit fullscreen mode

我将我们的`ExampleModel`类保存到一个名为 **models.py** 的文件中，这是我们导入 **Base** 的地方。一旦我们创建了我们的引擎，行`Base.metadata.create_all(engine)`就会创建与我们的模型相关的所有表。

查看我们的示例模型产生的 SQL 查询:

```
-- Sequence and defined type
CREATE SEQUENCE IF NOT EXISTS example.example_table_id_seq;

-- Table Definition
CREATE TABLE "example"."example_table" (
    "id" int4 NOT NULL DEFAULT nextval('example.example_table_id_seq'::regclass),
    "name" varchar(100) NOT NULL,
    "description" text,
    "join_date" timestamp NOT NULL,
    "vip" bool NOT NULL,
    "number" float8 NOT NULL,
    "data" float8 NOT NULL,
    PRIMARY KEY ("id")
); 
```

Enter fullscreen mode Exit fullscreen mode

### 加成:增加一条记录

我知道我们已经讨论过这个问题，但是*以防万一:*下面是我们如何添加一个`ExampleModel`的实例:

```
newModel = ExampleModel(name='todd',
                        description='im testing this',
                        vip=True,
                        join_date=datetime.now())
session.add(newModel)
session.commit()
print(newModel) 
```

Enter fullscreen mode Exit fullscreen mode

这个脚本应该返回`<Example model 1>`，因为我们向数据模型类添加了一个`__repr__`方法:

```
<Example model 1> 
```

Enter fullscreen mode Exit fullscreen mode

## 一对多&多对一关系

一对多(或多对一)关系可能是最常见的数据库关系类型。这些例子包括*客户+订单*关系(单个客户有多个订单)，或者*运动员+团队*关系(一名运动员属于一个团队)。我们将构建几个模型来帮助演示后者。

我将在下面贴一段代码，乍一看可能没什么意义。*没关系-* 任何人都不会自然而然地拥有这些。_ 我们将一起经历这一切:

```
from sqlalchemy import Column, Integer, String, Text, DateTime, Float, Boolean, ForeignKey
from sqlalchemy.orm import relationship
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()

class PlayerModel(Base):
    """Data model for players."""
    __tablename__ = "sqlalchemy_tutorial_players"
    __table_args__ = {"schema": "example"}

    id = Column(Integer,
                primary_key=True,
                nullable=False)
    team_id = Column(Integer,
                     ForeignKey('example.sqlalchemy_tutorial_teams.id'),
                     nullable=False)
    name = Column(String(100),
                  nullable=False)
    position = Column(String(100),
                      nullable=False)

    # Relationships
    team = relationship("TeamModel")

    def __repr__ (self):
        return '<Person model {}>'.format(self.id)

class TeamModel(Base):
    """Data model for people."""
    __tablename__ = "sqlalchemy_tutorial_teams"
    __table_args__ = {"schema": "example"}

    id = Column(Integer,
                primary_key=True,
                nullable=False)
    name = Column(String(100),
                  nullable=False)
    city = Column(String(100),
                  nullable=False)

    def __repr__ (self):
        return '<Team model {}>'.format(self.id) 
```

Enter fullscreen mode Exit fullscreen mode

有几件事我们*马上做*认可。我们有两个由`Column` s 组成的模型:一个模型用于*玩家、*，另一个模型用于*团队。这里有两个新增内容。*

首先，我们有了**外键**的概念(设置在`PlayerModel`的`team_id`列上)。如果您熟悉 SQL，您应该很容易上手。如果不是，请这样想:外键是列的属性。当一个外键存在时，我们说这个特定的列表示表之间的一个*关系*:一个表的大多数常见项目“属于”另一个表的项目，比如当*客户*【拥有】*订单、*或者当*团队*【拥有】*玩家时。*在我们的例子中，我们说每个*玩家*都有一个由他们的`team_id`指定的*团队*。这样，我们可以将我们的*玩家*表和我们的*团队*表之间的数据结合起来。

这里的另一个新概念是**关系。**关系补充了外键，是告诉我们的应用程序(不是我们的数据库)我们正在两个模型之间建立关系的一种方式。注意我们的外键的值是怎样的:`'example.sqlalchemy_tutorial_teams.id'` : **示例**是我们的 Postgres 模式，而**sqlalchemy _ tutorial _ teams**是我们的 *teams* 表的表名。将这个与我们传递给我们的**关系**的值进行比较，这个值是`"TeamModel"`:目标数据模型的类名(不是表名！).外键告诉 *SQL* 我们正在建立哪些关系，而关系告诉我们的*应用*我们正在建立哪些关系。我们需要双管齐下。

所有这些都是为了能够在我们的应用程序中轻松执行连接。当使用 ORM 时，我们不能说“用那个模型连接这个模型”，因为我们的应用程序不知道在上连接哪些列*。当在模型中指定了我们的关系时，我们可以做一些事情，比如将两个表连接在一起，而不需要指定任何进一步的细节:SQLAlchemy 将通过查看我们在数据模型中设置的内容(由我们设置的外键&关系强制执行)来知道如何连接表/模型。我们实际上是在通过预先定义关系来创建应用程序的业务逻辑的同时，为自己节省了处理数据相关逻辑的负担。*

SQLAlchemy 只在表不存在的情况下从数据模型创建表。换句话说，如果我们第一次运行我们的应用程序时有错误的关系，错误消息将在我们第二次运行我们的应用程序时继续存在，即使我们认为我们已经解决了问题。要处理奇怪的错误消息，每当对模型进行更改时，尝试在再次运行应用程序之前删除 SQL 表。

### 回溯引用

在数据模型上指定关系允许我们通过原始模型上的属性来访问连接模型的属性。如果我们将**玩家模型**与**团队模型**结合，我们将能够通过`PlayerModel.team.name`访问玩家团队的属性，其中`team`是我们关系的名称，`name`是相关模型的属性。

以这种方式创建的关系是单向的，因为我们可以通过一个玩家访问团队的详细信息，但是*不能从团队中访问玩家的详细信息。我们可以通过设置一个**反向参考**来轻松解决这个问题。*

在创建关系时，我们可以传递一个名为 **backref** 的属性，使关系成为双向的。下面是我们如何修改之前设置的关系:

```
 # Relationships team = relationship("TeamModel", backref="player") 
```

Enter fullscreen mode Exit fullscreen mode

有了 **backref** 的存在，我们现在可以通过调用`TeamModel.player`来访问一个团队的球员详细信息。

### 执行一个连接

一旦成功实现了两个数据模型之间的关系，检查工作的最佳方式就是对这些模型执行连接。我们不会浪费时间在这里创建高级 SQLAlchemy ORM 查询，但至少我们可以检查我们的工作:

```
def join_example():
    records = session.query(PlayerModel).\
        join(TeamModel, TeamModel.id == PlayerModel.team_id).all()
    for record in records:
        recordObject = {'name': record.name,
                        'position': record.position,
                        'team_name': record.team.name,
                        'team_city': record.team.city}
        print(recordObject) 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们在 **PlayerModel** 上加入 **TeamModel** ，因此我们将所有内容作为 **PlayerModel** 的属性进行引用。下面是示例数据的输出结果:

```
{'name': 'Joe McMan', 'position': 'Quarterback', 'team_name': 'The Piggers', 'team_city': 'Austin, TX'} 
```

Enter fullscreen mode Exit fullscreen mode

## 多对多关系

当我们希望我们的关系中的一个表在另一个表中的多个记录中只有一个记录时，设置外键关系很好地服务了我们(例如:每个队一个球员)。如果玩家可以属于*多个*团队会怎么样？这就是事情变得复杂的地方。

正如您可能已经猜到的，多对多关系发生在表之间，表 1 中的 n 条记录可能与表 2 中的 n 条记录相关联。SQLAlchemy 通过*关联表实现这样的关系。*关联表是专为解释这些关系而创建的 SQL 表，我们将创建一个关联表。

看看我们是如何定义下面的**关联表**变量的:

```
from sqlalchemy import Column, Integer, String, ForeignKey, Table
from sqlalchemy.orm import relationship
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()

association_table = Table('association', Base.metadata,
    Column('team_id', Integer, ForeignKey('example.sqlalchemy_tutorial_players.team_id')),
    Column('id', Integer, ForeignKey('example.sqlalchemy_tutorial_teams.id'))
)

class PlayerModel(Base):
    """Data model for players."""
    __tablename__ = "sqlalchemy_tutorial_players"
    __table_args__ = {"schema": "example"}

    id = Column(Integer,
                primary_key=True,
                unique=True,
                nullable=False)
    team_id = Column(Integer,
                     ForeignKey('example.sqlalchemy_tutorial_teams.id'),
                     nullable=False)
    name = Column(String(100),
                  nullable=False)
    position = Column(String(100),
                      nullable=False)

    # Relationships
    teams = relationship("TeamModel",
                         secondary=association_table,
                         backref="player")

    def __repr__ (self):
        return '<Player model {}>'.format(self.id)

class TeamModel(Base):
    """Data model for people."""
    __tablename__ = "sqlalchemy_tutorial_teams"
    __table_args__ = {"schema": "example"}

    id = Column(Integer,
                primary_key=True,
                unique=True,
                nullable=False)
    name = Column(String(100),
                  nullable=False)
    city = Column(String(100),
                  nullable=False)

    def __repr__ (self):
        return '<Team model {}>'.format(self.id) 
```

Enter fullscreen mode Exit fullscreen mode

我们使用一种新的数据类型**表**来定义一个建立多对多关联的表。我们传递的第一个参数是结果表的名称，我们将其命名为`association`。接下来，我们通过`Base.metadata`将我们的表与我们的数据模型扩展的相同声明基础相关联。最后，我们创建两列作为我们关联的每个表的外键:我们将**玩家模型**的**团队 id** 列与**团队模型**的 **id** 列链接起来。

我们在这里真正做的事情的本质是创建第三个表，它将我们的两个表关联起来。我们还可以通过创建第三个数据模型来实现这一点，但是创建关联表更简单一些。从现在开始，我们可以直接查询 **association_table** 来从我们的球员和球队表中获取记录。

实现关联表的最后一步是在我们的数据模型上设置一个关系。注意我们是如何像以前一样在 **PlayerModel** 上设置**关系**的，但是这次我们将**辅助**属性设置为关联表的名称。
# SQLAlchemy 性能反模式及其修复

> 原文：<https://dev.to/zchtodd/sqlalchemy-performance-anti-patterns-and-their-fixes-4bmm>

ORM 让应用程序开发人员的生活更加轻松。SQLAlchemy 提供了一个成熟的 ORM，它带来了模型关系、强大的查询构造范例、简单的序列化等好处。然而，由于这种易用性，人们很容易忘记幕后发生的事情。使用 SQLAlchemy 时做出的看似很小的选择可能会对性能产生重要影响。

在本文中，我将回顾开发人员在使用 SQLAlchemy 时遇到的一些主要性能问题。

#### 当只需要计数时，检索整个结果集

有时，开发人员只需要结果的计数，但不是利用数据库计数，而是获取所有结果，并通过 Python 中的 len 进行计数。

```
count = len(User.query.filter_by(acct_active=True).all()) 
```

使用 SQLAlchemy count 方法将导致在服务器端执行计数，从而导致发送到客户端的数据少得多。在前面的例子中调用 all()还会导致模型对象的实例化，如果有足够多的行，这很快就会变得昂贵。

除非需要更多的计数，否则选择使用计数方法。

```
count = User.query.filter_by(acct_active=True).count() 
```

#### 仅需要几列时检索整个模型

在许多情况下，发出查询时只需要几列。SQLAlchemy 可以只获取我们感兴趣的列，而不是返回整个模型实例。这不仅减少了发送的数据量，还避免了实例化整个对象的需要。使用列数据的元组而不是模型可以快得多。

```
result = User.query.all()
for user in result:
    print(user.name, user.email) 
```

相反，选择使用 with_entities 方法只选择需要的内容。

```
result = User.query.with_entities(User.name, User.email).all()
for (username, email) in result:
    print(username, email) 
```

#### 在循环内一次更新一个对象

避免使用循环一次更新一个集合。虽然数据库可以非常快速地执行一次更新，但是应用程序和数据库服务器之间的往返时间会很快增加。一般来说，在合理的情况下，尽量减少查询。

```
for user in users_to_update:
  user.acct_active = True
  db.session.add(user) 
```

请改用批量更新方法。

```
query = User.query.filter(user.id.in_([user.id for user in users_to_update]))
query.update({"acct_active": True}, synchronize_session=False) 
```

#### 触发级联删除

ORM 允许在模型上轻松配置关系，但是有一些微妙的行为可能会令人惊讶。大多数数据库通过外键和各种级联选项来维护关系完整性。SQLAlchemy 允许您用外键和级联选项定义模型，但是 ORM 有自己的级联逻辑，可以抢占数据库。

考虑以下模型。

```
class Artist(Base):
    __tablename__ = "artist"

    id = Column(Integer, primary_key=True)
    songs = relationship("Song", cascade="all, delete")

class Song(Base):
    __tablename__ = "song"

    id = Column(Integer, primary_key=True)
    artist_id = Column(Integer, ForeignKey("artist.id", ondelete="CASCADE")) 
```

删除艺术家将导致 ORM 对歌曲表发出删除查询，从而防止外键导致的删除。对于复杂的关系和足够多的记录，这种行为会成为瓶颈。

包括 passive_deletes 选项，以确保数据库正在管理关系。但是，请确保您的数据库能够做到这一点。例如，SQLite 默认情况下不管理外键。

```
songs = relationship("Song", cascade="all, delete", passive_deletes=True) 
```

#### 依赖惰性加载时应该使用急切加载

延迟加载是关系的默认 SQLAlchemy 方法。这意味着，从上一个例子开始，加载一个艺术家并不同时加载他或她的歌曲。这通常是个好主意，但是如果总是需要加载某些关系，那么单独的查询可能会造成浪费。

如果允许以懒惰的方式加载关系，像 Marshmallow 这样的流行序列化框架可以触发一连串的查询。

有几种方法可以控制这种行为。最简单的方法是通过关系函数本身。

```
songs = relationship("Song", lazy="joined", cascade="all, delete") 
```

这将导致一个左连接被添加到对艺术家的任何查询中，结果，歌曲集合将立即可用。尽管更多的数据被返回到客户端，但是往返可能会少得多。

SQLAlchemy 为无法采用这种一揽子方法的情况提供了更细粒度的控制。joinedload()函数可用于在每个查询的基础上切换联合加载。

```
from sqlalchemy.orm import joinedload

artists = Artist.query.options(joinedload(Artist.songs))
print(artists.songs) # Does not incur a roundtrip to load 
```

#### 使用 ORM 进行批量记录导入

当导入数千条记录时，构建完整模型实例的开销成为主要瓶颈。例如，想象一下，从一个文件中加载数千首歌曲记录，其中每首歌曲都首先被转换为一个字典。

```
for song in songs:
    db.session.add(Song(**song)) 
```

相反，绕过 ORM，只使用核心 SQLAlchemy 的参数绑定功能。

```
batch = []
insert_stmt = Song.__table__.insert()
for song in songs:
    if len(batch) > 1000:
       db.session.execute(insert_stmt, batch)
       batch.clear()
    batch.append(song)
if batch:
    db.session.execute(insert_stmt, batch) 
```

请记住，这个方法自然会跳过您可能依赖的任何客户端 ORM 逻辑，比如基于 Python 的列默认值。虽然此方法比将对象作为完整模型实例加载要快，但您的数据库可能有更快的大容量加载方法。例如，PostgreSQL 的 COPY 命令可能为加载大量记录提供了最佳性能。

#### 过早调用提交或刷新

在许多情况下，您需要将子记录与其父记录相关联，反之亦然。一个显而易见的方法是刷新会话，以便为有问题的记录分配一个 ID。

```
artist = Artist(name="Bob Dylan")
song = Song(title="Mr. Tambourine Man")

db.session.add(artist)
db.session.flush()

song.artist_id = artist.id 
```

每个请求多次提交或刷新通常是不必要的，也是不可取的。数据库刷新包括在数据库服务器上强制磁盘写入，在大多数情况下，客户端会一直阻塞，直到服务器确认数据已经写入。

SQLAlchemy 可以在幕后跟踪关系和管理密钥。

```
artist = Artist(name="Bob Dylan")
song = Song(title="Mr. Tambourine Man")

artist.songs.append(song) 
```

#### 包装完毕

我希望这个常见陷阱的列表可以帮助您避免这些问题，并保持您的应用程序平稳运行。与往常一样，在诊断性能问题时，度量是关键。大多数数据库都提供了性能诊断，可以帮助您查明问题，例如 PostgreSQL pg_stat_statements 模块。
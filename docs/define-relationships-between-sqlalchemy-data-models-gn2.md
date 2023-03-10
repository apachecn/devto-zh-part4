# 定义 SQLAlchemy 数据模型之间的关系

> 原文：<https://dev.to/hackersandslackers/define-relationships-between-sqlalchemy-data-models-gn2>

[![Define Relationships Between SQLAlchemy Data Models](img/660fe60f94c9539049e0120d992087da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CwYnNg4d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hackersandslackers-cdn.storage.googleapis.com/2019/07/sqlalchemy-relationships.jpg)

所有 Python 开发人员都可以从 SQLAlchemy 中获益。无论您是在寻找一种更好的方式来管理数据库连接(T2)，还是为您的应用程序构建一个 ORM(T4)数据层，我们都没有理由从 Python 词汇中省略掉“T0”。

从事大规模应用程序工作的工程师绝大多数更喜欢通过 ORM 而不是原始 SQL 来处理数据。对于那些有大量数据背景的人(像我一样)，将 SQL 隐藏在 Python 对象后面的抽象可能会令人不快。为什么我们*需要*外键来执行两个表之间的连接？为什么从事大型软件工作的工程师似乎过度使用诸如“**一对多**”与“**多对多**”关系之类的术语，而 SQL 本身却没有这样的术语？如果你感受到了这种情绪，你就在好公司里。

几年后，我开始发现 ORM*do*在构建应用程序的上下文中减少了工作量，并且不仅仅是“害怕 SQL”的人的拐杖通过将敏感数据事务作为可复制的代码模式来处理，我们节省了大量时间，但这种好处与我们在安全性和完整性方面获得的好处相比就相形见绌了。ORM 不写破坏性的 SQL 查询；人们会的。

所以，是的。编写 ORM 的工程师使用与理解底层 SQL 的工程师不同的行话，这*令人恼火，设置一个 ORM 需要多少前期工作，这*令人恼火，但*是*值得的。今天，我们通过学习如何在 SQLAlchemy 中定义表关系，解决了 ORM 开发中最困难的部分。

## 建立一些数据模型

我们已经在上一篇文章中介绍了 SQLAlchemy 数据模型，所以我将跳过更详细的细节。如果您是通过疯狂地在 Googling 上搜索关于 SQLAlchemy 的问题来到这里的，那么您可能应该了解什么是模型以及如何定义它们。

我们将创建几个模型来演示如何在它们之间创建 SQL 关系。本着博客的精神，我们将为`User`、`Post`和`Comment` :
创建模型

<figure>

```
"""Declare models and relationships."""
from sqlalchemy import Boolean, Column, DateTime, ForeignKey, Integer, String, Text
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import relationship
from sqlalchemy.sql import func

from database import engine

Base = declarative_base()

class User(Base):
    """User account."""

    __tablename__ = "user"

    id = Column(Integer, primary_key=True, autoincrement="auto")
    username = Column(String(255), unique=True, nullable=False)
    password = Column(Text, nullable=False)
    email = Column(String(255), unique=True, nullable=False)
    first_name = Column(String(255))
    last_name = Column(String(255))
    bio = Column(Text)
    avatar_url = Column(Text)
    role = Column(String(255))
    last_seen = Column(DateTime)
    created_at = Column(DateTime, server_default=func.now())
    updated_at = Column(DateTime, onupdate=func.now())

    def __repr__ (self):
        return "<User %r>" % self.username

class Comment(Base):
    """User-generated comment on a blog post."""

    __tablename__ = "comment"

    id = Column(Integer, primary_key=True, index=True)
    user_id = Column(Integer)
    post_id = Column(Integer, index=True)
    body = Column(Text)
    upvotes = Column(Integer, default=1)
    removed = Column(Boolean, default=False)
    created_at = Column(DateTime, server_default=func.now())

    def __repr__ (self):
        return "<Comment %r>" % self.id

class Post(Base):
    """Blog post/article."""

    __tablename__ = "post"

    id = Column(Integer, primary_key=True, index=True)
    author_id = Column(Integer)
    slug = Column(String(255), nullable=False, unique=True)
    title = Column(String(255), nullable=False)
    summary = Column(String(400))
    feature_image = Column(String(300))
    body = Column(Text)
    status = Column(String(255), nullable=False, default="unpublished")
    created_at = Column(DateTime, server_default=func.now())
    updated_at = Column(DateTime, server_default=func.now())

    def __repr__ (self):
        return "<Post %r>" % self.slug

Base.metadata.create_all(engine) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>models.py</figcaption>

</figure>

## 一对多关系

一对多(或多对一)关系是最常见的数据库关系类型。如何应用这种关系的一个永恒的例子是 ***客户&订单之间的业务关系。*** 单个客户有多个订单，但订单没有多个客户，故名

以我们的博客为例，让我们看看对于拥有多个帖子的作者，或者拥有多个评论的帖子，一对多的关系是什么样子的:

<figure>

```
...

from sqlalchemy.orm import relationship

class User(Base):
    """User account."""

    __tablename__ = "user"

    id = Column(Integer, primary_key=True, autoincrement="auto")
    username = Column(String(255), unique=True, nullable=False)
    password = Column(Text, nullable=False)
    email = Column(String(255), unique=True, nullable=False)
    first_name = Column(String(255))
    last_name = Column(String(255))
    bio = Column(Text)
    avatar_url = Column(Text)
    role = Column(String(255))
    last_seen = Column(DateTime)
    created_at = Column(DateTime, server_default=func.now())
    updated_at = Column(DateTime, onupdate=func.now())

    def __repr__ (self):
        return "<User %r>" % self.username

class Comment(Base):
    """User-generated comment on a blog post."""

    __tablename__ = "comment"

    id = Column(Integer, primary_key=True, index=True)
    user_id = Column(Integer, ForeignKey("user.id")) # Foreign key added
    post_id = Column(Integer, ForeignKey("post.id"), index=True) # Foreign key added
    body = Column(Text)
    upvotes = Column(Integer, default=1)
    removed = Column(Boolean, default=False)
    created_at = Column(DateTime, server_default=func.now())

    # Relationships
    user = relationship("User")

    def __repr__ (self):
        return "<Comment %r>" % self.id

class Post(Base):
    """Blog post/article."""

    __tablename__ = "post"

    id = Column(Integer, primary_key=True, index=True)
    author_id = Column(Integer, ForeignKey("user.id")) # Foreign key added
    slug = Column(String(255), nullable=False, unique=True)
    title = Column(String(255), nullable=False)
    summary = Column(String(400))
    feature_image = Column(String(300))
    body = Column(Text)
    status = Column(String(255), nullable=False, default="unpublished")
    created_at = Column(DateTime, server_default=func.now())
    updated_at = Column(DateTime, server_default=func.now())

    # Relationships
    author = relationship("User")
    comments = relationship("Comment")

    def __repr__ (self):
        return "<Post %r>" % self.slug 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>models.py</figcaption>

</figure>

我们在模型中添加了两个重要的附加因素，乍一看可能很难发现。首先，我们将一些属性(列)设置为**外键**(如果您熟悉 SQL，应该很容易理解)。外键是列的属性；当一个外键存在时，我们说这个特定的列表示表之间的一个*关系*:一个表的最常见的项目“属于”另一个表的项目，比如当*客户*【拥有】*订单、*或者当*用户*【拥有】*帖子时。*在我们的例子中，我们说每个*帖子*都有一个由`author_id`属性指定的*作者(用户)*，如下所示:

```
author_id = Column(Integer, ForeignKey("user.id")) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将我们的 *users* 表和我们的 *posts* 表之间的数据结合起来，这样获取一个表就可以让我们获得另一个表的信息。

这里的另一个新概念是**关系。**关系补充了外键，是告诉我们的应用程序(不是我们的数据库)我们正在两个模型之间建立关系的一种方式。注意我们的外键的值是`'user.id'`。`user`是我们的`User`表的表名。将这个值与我们传递给我们的**关系**的值进行比较，这个值是`"User"`:目标数据模型的类名(不是表名！).

```
author = relationship("User") 
```

Enter fullscreen mode Exit fullscreen mode

外键告诉 *SQL* 我们正在建立哪些关系，而关系告诉我们的*应用*我们正在建立哪些关系。我们需要双管齐下。

所有这些都是为了能够在我们的应用程序中轻松执行连接。当使用 ORM 时，我们不能说“用那个模型连接这个模型”，因为我们的应用程序不知道在上连接哪些列*。当在模型中指定了我们的关系时，我们可以做一些事情，比如将两个表连接在一起，而不需要指定任何进一步的细节:SQLAlchemy 将通过查看我们在数据模型中设置的内容(由我们设置的外键&关系强制执行)来知道如何连接表/模型。我们实际上是在通过预先定义关系来创建应用程序的业务逻辑的同时，为自己节省了处理数据相关逻辑的负担。*

SQLAlchemy 只在表不存在的情况下从数据模型创建表。换句话说，如果我们第一次运行我们的应用程序时有错误的关系，错误消息将在我们第二次运行我们的应用程序时继续存在，即使我们认为我们已经解决了问题。要处理奇怪的错误消息，每当对模型进行更改时，尝试在再次运行应用程序之前删除 SQL 表。

### 回溯引用

在数据模型上指定关系允许我们通过原始模型上的属性来访问连接模型的属性。如果我们要将我们的**评论**模型与我们的**用户**模型连接起来，我们将能够通过`Comment.user.username`访问评论作者的属性，其中`user`是我们关系的名称，`username`是关联模型的属性。

以这种方式创建的关系是单向的，因为我们可以通过一个玩家访问团队的详细信息，但是*不能从团队中访问玩家的详细信息。我们可以通过设置一个**反向参考**来轻松解决这个问题。*

在创建关系时，我们可以传递一个名为 **backref** 的属性，使关系成为双向的。下面是我们如何修改之前设置的关系:

<figure>

```
 # Relationships author = relationship("User", backref="posts") 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>models.py</figcaption>

</figure>

有了 **backref** 的存在，我们现在可以通过调用`Post.author`来访问帖子的用户详细信息。

### 创建相关数据

是时候创建一些数据了。我们需要一个用户作为我们博客的作者。既然这样，让我们给他们一些博客帖子:

<figure>

```
from .models import Post, User

admin_user = User(
    username="toddthebod",
    password="Password123lmao",
    email="todd@example.com",
    first_name="Todd",
    last_name="Birchard",
    bio="I write tutorials on the internet.",
    avatar_url="https://storage.googleapis.com/hackersandslackers-cdn/authors/todd_small@2x.jpg",
    role="admin",
)

post_1 = Post(
    author_id=admin_user.id,
    slug="fake-post-slug",
    title="Fake Post Title",
    status="published",
    summary="A fake post to have some fake comments.",
    feature_image="https://hackersandslackers-cdn.storage.googleapis.com/2021/01/logo-smaller@2x.png",
    body="Cheese slices monterey jack cauliflower cheese dolcelatte cheese and wine fromage frais rubber cheese gouda. Rubber cheese cheese and wine cheeseburger cheesy grin paneer paneer taleggio caerphilly. Edam mozzarella.",
)

post_2 = Post(
    author_id=admin_user.id,
    slug="an-additional-post",
    title="Yet Another Post Title",
    status="published",
    summary="An in-depth exploration into writing your second blog post.",
    feature_image="https://hackersandslackers-cdn.storage.googleapis.com/2021/01/logo-smaller@2x.png",
    body="Smelly cheese cheese slices fromage. Pepper jack taleggio monterey jack cheeseburger pepper jack swiss everyone loves. Cheeseburger say cheese brie fromage frais swiss when the cheese comes out everybody's happy babybel cheddar. Cheese and wine cheesy grin",
) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>objects.py</figcaption>

</figure>

我们创建了我们的对象，但是我们还没有将它们保存到数据库中。我将为此创建几个函数。`create_user()`将处理用户创建，而`create_post()`将创建...嗯，你知道:

<figure>

```
"""Create records related to one another via SQLAlchemy's ORM."""
from typing import Tuple

from sqlalchemy.exc import IntegrityError, SQLAlchemyError
from sqlalchemy.orm import Session

from logger import LOGGER
from sqlalchemy_tutorial.part3_relationships.models import Post, User

def create_user(session: Session, user: User) -> User:
    """
    Create a new user if username isn't already taken.

    :param session: SQLAlchemy database session.
    :type session: Session
    :param user: New user record to create.
    :type user: User

    :return: Optional[User]
    """
    try:
        existing_user = session.query(User).filter(User.username == user.username).first()
        if existing_user is None:
            session.add(user) # Add the user
            session.commit() # Commit the change
            LOGGER.success(f"Created user: {user}")
        else:
            LOGGER.warning(f"Users already exists in database: {existing_user}")
        return session.query(User).filter(User.username == user.username).first()
    except IntegrityError as e:
        LOGGER.error(e.orig)
        raise e.orig
    except SQLAlchemyError as e:
        LOGGER.error(f"Unexpected error when creating user: {e}")
        raise e

def create_post(session: Session, post: Post) -> Post:
    """
    Create a post.

    :param session: SQLAlchemy database session.
    :type session: Session
    :param post: Blog post to be created.
    :type post: Post

    :return: Post
    """
    try:
        existing_post = session.query(Post).filter(Post.slug == post.slug).first()
        if existing_post is None:
            session.add(post) # Add the post
            session.commit() # Commit the change
            LOGGER.success(
                f"Created post {post} published by user {post.author.username}"
            )
            return session.query(Post).filter(Post.slug == post.slug).first()
        else:
            LOGGER.warning(f"Post already exists in database: {post}")
            return existing_post
    except IntegrityError as e:
        LOGGER.error(e.orig)
        raise e.orig
    except SQLAlchemyError as e:
        LOGGER.error(f"Unexpected error when creating user: {e}")
        raise e 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>orm.py</figcaption>

</figure>

如果您被这些函数的复杂性弄得措手不及，要知道除了`session.add()`和`session.commit()`之外的一切都是为了错误处理和避免重复而存在的。我们不想要重复的帖子或用户，因此我们在继续各自的功能之前检查了`existing_user`和`existing_post`。

让我们创造这些记录:

<figure>

```
from .orm import create_user, create_post

# Create admin user & two posts admin_user = create_user(session, admin_user)
post_1 = create_post(session, post_1)
post_2 = create_post(session, post_2) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Creating records</figcaption>

</figure>

我们现在有**一个**用户，**很多**(大概)帖子！如果您检查您的数据库，您会看到这些记录是在那里创建的。为一个**一对多**连接查询做好了准备。

### 执行一对多连接

当我们在 SQLAlchemy 模型上执行连接时，我们可以利用我们获取的每个记录的*关系*属性，就好像该属性本身就是一个完整的记录一样。向你展示我的意思可能更容易。

在下面的查询中，我们获取数据库中属于**用户 1** 的所有帖子。然后，我们通过用`.join(User, Post.author_id == User.id)` :
扩展我们的查询来加入属于那个用户的帖子

```
"""Perform JOIN queries on models with relationships."""
from sqlalchemy.orm import Session

from logger import LOGGER
from sqlalchemy_tutorial.part3_relationships.models import Post, User

def get_all_posts(session: Session, admin_user: User):
    """
    Fetch all posts belonging to an author user.

    :param session: SQLAlchemy database session.
    :type session: Session
    :param admin_user: Author of blog posts.
    :type admin_user: User

    :return: None
    """
    posts = (
        session.query(Post)
        .join(User, Post.author_id == User.id)
        .filter_by(username=admin_user.username)
        .all()
    )
    for post in posts:
        post_record = {
            "post_id": post.id,
            "title": post.title,
            "summary": post.summary,
            "status": post.status,
            "feature_image": post.feature_image,
            "author": {
                "id": post.author_id,
                "username": post.author.username,
                "first_name": post.author.first_name,
                "last_name": post.author.last_name,
                "role": post.author.role,
            },
        }
        LOGGER.info(post_record) 
```

Enter fullscreen mode Exit fullscreen mode

执行完查询后，我们循环遍历每条记录，并打印一个 JSON 对象，该对象表示我们获取的文章及其作者的数据:

<figure>

```
{  "post_id":  1,  "title":  "Fake Post Title",  "summary":  "A fake post to have some fake comments.",  "status":  "published",  "feature_image":  "https://hackersandslackers-cdn.storage.googleapis.com/2021/01/logo-smaller@2x.png",  "author":  {  "id":  2,  "username":  "toddthebod",  "first_name":  "Todd",  "last_name":  "Birchard",  "role":  "admin"  }  },  {  "post_id":  2,  "title":  "Yet Another Post Title",  "summary":  "An in-depth exploration into writing your second blog post.",  "status":  "published",  "feature_image":  "https://hackersandslackers-cdn.storage.googleapis.com/2021/01/logo-smaller@2x.png",  "author":  {  "id":  2,  "username":  "toddthebod",  "first_name":  "Todd",  "last_name":  "Birchard",  "role":  "admin"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Output of `get_all_posts()`</figcaption>

</figure>

## 多对多关系

当我们希望我们的关系中的一个表在另一个表中的多个记录中只有一个记录时，设置外键关系很好地服务了我们(例如:每个队一个球员)。如果玩家可以属于*多个*团队会怎么样？这就是事情变得复杂的地方。

正如您可能已经猜到的，多对多关系发生在表之间，表 1 中的 n 条记录可能与表 2 中的 n 条记录相关联。SQLAlchemy 通过*关联表实现这样的关系。*关联表是专为解释这些关系而创建的 SQL 表，我们将创建一个关联表。

看看我们如何定义下面的**关联表**变量:

<figure>

```
from sqlalchemy import Column, Integer, String, ForeignKey, Table
from sqlalchemy.orm import relationship
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()

association_table = Table('association', Base.metadata,
    Column('team_id', Integer, ForeignKey('example.sqlalchemy_tutorial_players.team_id')),
    Column('id', Integer, ForeignKey('example.sqlalchemy_tutorial_teams.id'))
)

class Player(Base):
    """Individual player belonging to a team."""

    __tablename__ = "player"

    id = Column(Integer, primary_key=True, autoincrement="auto")
    team_id = Column(Integer, ForeignKey("team.id"), nullable=False)
    first_name = Column(String(255), nullable=False)
    last_name = Column(String(255), nullable=False)
    position = Column(String(100), nullable=False)
    injured = Column(Boolean)
    description = Column(Text, nullable=True)
    created_at = Column(DateTime, server_default=func.now())
    updated_at = Column(DateTime, onupdate=func.now())

    # Relationships
    team = relationship("Team")

    def __repr__ (self):
        return "<Player {}>".format(self.id)

class Team(Base):
    """Team consisting of many players."""

    __tablename__ = "team"

    id = Column(Integer, primary_key=True, autoincrement="auto")
    name = Column(String(255), nullable=False)
    city = Column(String(255), nullable=False)
    created_at = Column(DateTime, server_default=func.now())
    updated_at = Column(DateTime, onupdate=func.now())

    def __repr__ (self):
        return "<Team {}>".format(self.id) 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>models.py</figcaption>

</figure>

我们使用一种新的数据类型**表**来定义一个建立多对多关联的表。我们传递的第一个参数是结果表的名称，我们将其命名为`association`。接下来，我们通过`Base.metadata`将我们的表与我们的数据模型扩展的相同声明基础相关联。最后，我们创建两列作为我们关联的每个表的外键:我们将**玩家**的 **team_id** 列与 **Team** 的 **id** 列链接起来。

我们在这里真正做的事情的本质是创建第三个表，它将我们的两个表关联起来。我们还可以通过创建第三个数据模型来实现这一点，但是创建关联表更简单一些。从现在开始，我们可以直接查询 **association_table** 来从我们的球员和球队表中获取记录。

实现关联表的最后一步是在我们的数据模型上设置一个关系。注意我们如何像以前一样在**玩家**上设置一个**关系**，但是这次我们将**次级**属性设置为关联表的名称。

<figure>

```
[  {  "comment_id":  1,  "body_summary":  "This post about SQLAlchemy is awful. You didnt ev...",  "upvotes":  2,  "comment_author_id":  3,  "post":  {  "slug":  "fake-post-slug",  "title":  "Fake Post Title",  "post_author":  "toddthebod"  }  },  {  "comment_id":  2,  "body_summary":  "By the way, you SUCK!!! I HATE you!!!! I have a pr...",  "upvotes":  5,  "comment_author_id":  3,  "post":  {  "slug":  "fake-post-slug",  "title":  "Fake Post Title",  "post_author":  "toddthebod"  }  },  {  "comment_id":  3,  "body_summary":  "YOU RUINED MY LIFE!!!!...",  "upvotes":  5,  "comment_author_id":  3,  "post":  {  "slug":  "fake-post-slug",  "title":  "Fake Post Title",  "post_author":  "toddthebod"  }  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>Record output</figcaption>

</figure>
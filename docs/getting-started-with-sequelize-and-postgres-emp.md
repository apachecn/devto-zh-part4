# Sequelize 和 Postgres 入门

> 原文：<https://dev.to/nedsoft/getting-started-with-sequelize-and-postgres-emp>

[Sequelize](https://github.com/sequelize/sequelize#sequelize) 是一个基于 promise 的 Node.js ORM，适用于 Postgres、MySQL、MariaDB、SQLite 和 Microsoft SQL Server。它具有可靠的事务支持、关系、急切加载和延迟加载、读取复制等特性。

> ORM 库是用你选择的语言编写的完全普通的库，它封装了操纵数据所需的代码，所以你不再使用 SQL 你可以用你正在使用的同一种语言直接与一个对象交互- [stackoverflow](https://stackoverflow.com/questions/1279613/what-is-an-orm-how-does-it-work-and-how-should-i-use-one)

尽管 Sequelize 支持其他几种数据库客户机，但本文主要讨论如何将 Sequelize 与 Postgres 结合使用。我们将建立一个简单的博客，用户可以创建帖子，查看帖子和添加评论。

本文分为两部分:

*   第一部分-顺序设置、配置、迁移和播种。
*   第二部分-用序列执行 CRUD。

### 要求

*   已安装的节点
*   安装了 npm 或纱线

## 第一部分

如果您尚未创建 express 项目，请快速创建并在终端中打开该项目。在项目的根文件夹中，运行以下命令来安装必要的依赖项:

```
npm install sequelize sequelize-cli pg pg-hstore 
```

Enter fullscreen mode Exit fullscreen mode

上面命令中安装的库的简要说明:

**序列**是序列库本身。

 是一个软件包，它使我们能够从 cli 通过`sequelize`与数据库进行交互。

[**pg**](https://www.npmjs.com/package/pg)postgres 的简称是 Node.js 的 Postgres 客户端

[**pg-hstore**](https://www.npmjs.com/package/pg-hstore) 是将 JSON 数据序列化和反序列化为 hstore 格式的节点包。

接下来，让我们创建序列配置，为此，运行下面的命令创建一个名为`.sequelizerc`
的文件

```
touch .sequelizerc 
```

Enter fullscreen mode Exit fullscreen mode

将下面的代码复制到`.seqluelizerc`文件:

```
const path = require('path')

module.exports = {
  config: path.resolve('./database/config', 'config.js'),
  'models-path': path.resolve('./database/models'),
  'seeders-path': path.resolve('./database/seeders'),
  'migrations-path': path.resolve('./database/migrations'),
} 
```

Enter fullscreen mode Exit fullscreen mode

Sequelize 使用指定的路径使用`.sequelizerc`文件来生成配置和模型。

接下来，我们通过运行下面的命令来生成配置:

```
sequelize init 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该有一个名为`database`的新目录，带有如下所示的脚手架:

[![](img/d988ac5eb0e5b301aebf4365c744a229.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BcI7VKl2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/./db-folder-struct.png)

接下来，我们来编辑一下`database/config/config.js`。
用下面的代码替换`database/config/config.js`的内容:

```
require('dotenv').config()

module.exports = {
  development: {
    url: process.env.DEV_DATABASE_URL,
    dialect: 'postgres',
  },
  test: {
    url: process.env.TEST_DATABASE_URL,
    dialect: 'postgres',
  },
  production: {
    url: process.env.DATABASE_URL,
    dialect: 'postgres',
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们的重点是在 Postgres 中使用 Sequelize，所以我们简化了 Postgres 的配置。

因为我们使用 Postgres，所以我们将使用连接字符串来连接数据库。创建两个 Postgres 数据库，一个用于开发，一个用于测试(如果您的项目需要测试)。

### 如何创建 Postgres 数据库连接字符串

如果您已经创建了数据库，可以跳到下一部分。

*方法 1*

如果您在本地安装了 Postgres，请按照下面的步骤创建一个数据库并生成连接字符串。打开终端并运行下面的命令:

```
createdb dev_db -U <db_user>
createdb test_db -U <db_user> 
```

Enter fullscreen mode Exit fullscreen mode

上述数据库的连接字符串将是:

```
postgres://<db_user>:<db_password>@127.0.0.1:5432/dev_db
postgres://<db_user>:<db_password>@127.0.0.1:5432/test_db 
```

Enter fullscreen mode Exit fullscreen mode

现在，创建一个`.env`文件，并将下面的代码片段复制到其中。

```
DATABASE_URL=
DEV_DATABASE_URL=postgres://<db_user>:<db_password>@127.0.0.1:5432/dev_db
TEST_DATABASE_URL=postgres://<db_user>:<db_password>@127.0.0.1:5432/test_db 
```

Enter fullscreen mode Exit fullscreen mode

请注意，如果您使用 Heroku 进行生产，一旦您添加了 Postgres 附加组件，Heroku 将生成一个连接字符串并注入到环境变量`DATABASE_URL`中。

*方法二*

如果本地没有安装 Postgres，可以使用 [ElephantSQL](https://www.elephantsql.com/) 来创建数据库。

### 创建模型和迁移

我们需要创建用户、帖子和评论模型。为此，运行以下命令:

```
sequelize model:generate --name User --attributes name:string,email:string

sequelize model:generate --name Post --attributes title:string,content:text,userId:integer

sequelize model:generate --name Comment --attributes postId:integer,comment:text,userId:integer 
```

Enter fullscreen mode Exit fullscreen mode

上面的每个命令将分别在`/database/migrations`和`database/models`目录下生成一个迁移和一个模型。

**注意**，确保`--attributes`定义之间没有空格。

例如，`--attributes postId:integer, comment:text, userId:integer`会因为属性之间的空白而抛出一个错误`ERROR: Attribute '' cannot be parsed: Cannot read property 'dataType' of undefined`。

接下来，我们需要对迁移和模型进行一些更改。
首先，我们需要向`FOREIGN_KEY`属性(userId，postId)添加`NOT NULL`约束。我第一次使用 Sequelize 时，我不知道这一点，而且急切加载的模型不工作。在迁移中编辑如下所示的`FOREIGN_KEY`属性:

```
userId: {
      type: Sequelize.INTEGER,
      allowNull: false,
    },

postId: {
      type: Sequelize.INTEGER,
      allowNull: false,
    }, 
```

Enter fullscreen mode Exit fullscreen mode

编辑`models/index.js`文件如下:

```
 const fs = require('fs');
const path = require('path');
const Sequelize = require('sequelize');
const envConfigs =  require('../config/config');

const basename = path.basename(__filename);
const env = process.env.NODE_ENV || 'development';
const config = envConfigs[env];
const db = {};

let sequelize;
if (config.url) {
  sequelize = new Sequelize(config.url, config);
} else {
  sequelize = new Sequelize(config.database, config.username, config.password, config);
}

fs
  .readdirSync(__dirname)
  .filter(file => {
    return (file.indexOf('.') !== 0) && (file !== basename) && (file.slice(-3) === '.js');
  })
  .forEach(file => {
    const model = sequelize['import'](path.join(__dirname, file));
    db[model.name] = model;
  });

Object.keys(db).forEach(modelName => {
  if (db[modelName].associate) {
    db[modelName].associate(db);
  }
});

db.sequelize = sequelize;
db.Sequelize = Sequelize;

module.exports = db;

//models/index.js 
```

Enter fullscreen mode Exit fullscreen mode

### 定义模型关系

我们有三个相互关联的模型，如下所示

*   一个用户有很多帖子，一个帖子属于一个用户(1:n)
*   一个用户有很多评论，一个评论属于一个用户(1:n)
*   一个帖子有很多评论，一个评论属于一个帖子(1:n)

为了实现以编程方式建立上述关系，让我们编辑模型如下:

```
module.exports = (sequelize, DataTypes) => {
  const User = sequelize.define('User', {
    name: DataTypes.STRING,
    email: DataTypes.STRING
  }, {});
  User.associate = function(models) {
    // associations can be defined here
    User.hasMany(models.Post, {
      foreignKey: 'userId',
      as: 'posts',
      onDelete: 'CASCADE',
    });

    User.hasMany(models.Comment, {
      foreignKey: 'userId',
      as: 'comments',
      onDelete: 'CASCADE',
    });
  };
  return User;
};

// database/models/user.js 
```

Enter fullscreen mode Exit fullscreen mode

```
module.exports = (sequelize, DataTypes) => {
  const Post = sequelize.define('Post', {
    title: DataTypes.STRING,
    content: DataTypes.TEXT,
    userId: DataTypes.INTEGER
  }, {});
  Post.associate = function(models) {
    // associations can be defined here
    Post.hasMany(models.Comment, {
      foreignKey: 'postId',
      as: 'comments',
      onDelete: 'CASCADE',
    });

    Post.belongsTo(models.User, {
      foreignKey: 'userId',
      as: 'author',
      onDelete: 'CASCADE',
    })
  };
  return Post;
};

// database/models/post.js 
```

Enter fullscreen mode Exit fullscreen mode

```
module.exports = (sequelize, DataTypes) => {
  const Comment = sequelize.define('Comment', {
    postId: DataTypes.INTEGER,
    comment: DataTypes.TEXT,
    userId: DataTypes.INTEGER
  }, {});
  Comment.associate = function(models) {
    // associations can be defined here
    Comment.belongsTo(models.User, {
      foreignKey: 'userId',
      as: 'author'
    });
    Comment.belongsTo(models.Post, {
      foreignKey: 'postId',
      as: 'post'
    });
  };
  return Comment;
};

// database/models/comment.js 
```

Enter fullscreen mode Exit fullscreen mode

是时候运行迁移了，它会将迁移转换成数据库中的表。运行

```
sequelize db:migrate 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，表格就会生成，我们就可以开始将数据存入数据库了。

### 将数据播种到数据库

让我们用一些虚拟数据填充数据库。运行下面的命令来为模型生成种子文件。

```
sequelize seed:generate --name User

sequelize seed:generate --name Post

sequelize seed:generate --name Comment 
```

Enter fullscreen mode Exit fullscreen mode

以上命令将分别为`User`、`Post`和`Comment`型号生成三个文件`xxxx-User.js`、`xxxx-Post.js`和`xxxx-Comment.js`。

编辑种子文件如下:

```
module.exports = {
  up: (queryInterface, Sequelize) => queryInterface.bulkInsert(
    'Users',
    [
      {
        name: 'Jane Doe',
        email: 'janedoe@example.com',
        createdAt: new Date(),
        updatedAt: new Date(),
      },
      {
        name: 'Jon Doe',
        email: 'jondoe@example.com',
        createdAt: new Date(),
        updatedAt: new Date(),
      },
    ],
    {},
  ),

  down: (queryInterface, Sequelize) => queryInterface.bulkDelete('Users', null, {}),
};

// database/seeds/xxxx-User.js 
```

Enter fullscreen mode Exit fullscreen mode

```
module.exports = {
  up: (queryInterface, Sequelize) =>
    queryInterface.bulkInsert(
      "Posts",
      [
        {
          userId: 1,
          title: "hispotan de nu",
          content:
            "Nulla mollis molestie lorem. Quisque ut erat. Curabitur gravida nisi at nibh.",
          createdAt: new Date(),
          updatedAt: new Date()
        },
        { 
          userId: 2,
          title: 'some dummy title',
          content:
            "Maecenas tincidunt lacus at velit. Vivamus vel nulla eget eros elementum pellentesque. Quisque porta volutpat erat.",
          createdAt: new Date(),
          updatedAt: new Date()
        }
      ],

      {}
    ),

  down: (queryInterface, Sequelize) =>
    queryInterface.bulkDelete("Posts", null, {})
};
// database/seeds/xxxx-Post.js 
```

Enter fullscreen mode Exit fullscreen mode

```
module.exports = {
  up: (queryInterface, Sequelize) =>
    queryInterface.bulkInsert(
      "Comments",
      [
        {
          userId: 1,
          postId: 2,
          comment:
            "Nulla mollis molestie lorem. Quisque ut erat. Curabitur gravida nisi at nibh.",
          createdAt: new Date(),
          updatedAt: new Date()
        },
        {
          userId: 2,
          postId: 1,
          comment:
            "Maecenas tincidunt lacus at velit. Vivamus vel nulla eget eros elementum pellentesque. Quisque porta volutpat erat.",
          createdAt: new Date(),
          updatedAt: new Date()
        }
      ],
      {}
    ),

  down: (queryInterface, Sequelize) =>
    queryInterface.bulkDelete("Comments", null, {})
};

// database/seeds/xxxx-Comment.js 
```

Enter fullscreen mode Exit fullscreen mode

现在，运行下面的命令来播种数据库:

```
sequelize db:seed:all 
```

Enter fullscreen mode Exit fullscreen mode

> 你可以在这里克隆这篇文章[的完整代码](https://github.com/oriechinedu/sequelize-with-postgres-tutorial.git)

哟！暂时就这样了。在本文的第二部分，我将在这篇文章的基础上实现博客的 CRUD。敬请期待！📌

#### 建议的资源

*   [对文件进行排序](https://sequelize.org/)
*   [什么是 ORM，为什么要使用它](https://blog.bitsrc.io/what-is-an-orm-and-why-you-should-use-it-b2b6f75f5e2a)

*本文最初发表在我的[博客](https://www.oriechinedu.com/posts/getting-started-with-sequelize-and-postgres/)T3 上*
# 用 Go 创建自以为是的 GraphQL 服务器-第 3 部分

> 原文：<https://dev.to/cmelgarejo/creating-an-opinionated-graphql-server-with-go-part-3-3aoi>

这是一个系列的一部分，不适合初学者，但也欢迎！你可以从一开始就检验它

*   [第一部分](https://dev.to/cmelgarejo/creating-an-opinionated-graphql-server-with-go-part-1-3g3l)

回顾如何到达这一部分，因为所有的代码都在 github 的第三部分

* * *

今天我们将把 GORM 和一个数据库添加到我们的项目中，我选择的 RDBMS
是 [PostgreSQL](https://www.postgresql.org) 你可以使用 MySQL，SQLite，任何你喜欢的东西
，只要 GORM 支持它。而且，如果你错过了，你也可以看看
[第二部](//PART2.md)。在
中，我会以更快的速度前进，更多信息请查看代码中的注释

## 向项目中添加 GORM

让我们将 GORM 添加到我们的项目中:

> `$ go get -u github.com/jinzhu/gorm`

因此，我们需要迁移，让我们添加 [Gomigrate](https://gopkg.in/gormigrate.v1)

> `$ go get gopkg.in/gormigrate.v1`

我不希望 id 像现在的`int` s [1，2，3]一样容易被发现
任何人都可以在`deleteUser(userId: 1)`上发送一个突变，这应该会增加
另一层复杂性，以减少我们愚蠢的 API 的攻击面。

从`go-frs`添加 UUID 包:

> `$ go get github.com/gofrs/uuid`

我们将设置数据库模型，使用 UUID 作为主键。

## 潜入戈姆

现在，让我们将`GORM`设置到我们的项目中，使用:

*   迁移
*   模型

首先让我们为所有的数据库创建一个入口点，在这里我们可以初始化和
设置数据库连接，让我们把这个代码放在`internal/orm/main.go`

```
// Package orm provides `GORM` helpers for the creation, migration and access
// on the project's database
package orm

import (
    log "github.com/cmelgarejo/go-gql-server/internal/logger"

    "github.com/cmelgarejo/go-gql-server/internal/orm/migration"

    "github.com/cmelgarejo/go-gql-server/pkg/utils"
    //Imports the database dialect of choice
    _ "github.com/jinzhu/gorm/dialects/postgres"

    "github.com/jinzhu/gorm"
)

var autoMigrate, logMode, seedDB bool
var dsn, dialect string

// ORM struct to holds the gorm pointer to db
type ORM struct {
    DB *gorm.DB
}

func init() {
    dialect = utils.MustGet("GORM_DIALECT")
    dsn = utils.MustGet("GORM_CONNECTION_DSN")
    seedDB = utils.MustGetBool("GORM_SEED_DB")
    logMode = utils.MustGetBool("GORM_LOGMODE")
    autoMigrate = utils.MustGetBool("GORM_AUTOMIGRATE")
}

// Factory creates a db connection with the selected dialect and connection string
func Factory() (*ORM, error) {
    db, err := gorm.Open(dialect, dsn)
    if err != nil {
        log.Panic("[ORM] err: ", err)
    }
    orm := &ORM{
        DB: db,
    }
    // Log every SQL command on dev, @prod: this should be disabled?
    db.LogMode(logMode)
    // Automigrate tables
    if autoMigrate {
        err = migration.ServiceAutoMigration(orm.DB)
    }
    log.Info("[ORM] Database connection initialized.")
    return orm, err
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能已经注意到了`internal/logger`日志包，我们将在
[奖励](#extra:-bonus-section)部分深入探讨，现在，你甚至可以使用`import "log"`包:)

### 迁移

迁移服务在`migration.ServiceAutoMigration`中调用，我们将在`internal/ocm/migration/main.go` :
中
保存

```
package migration

import (
    "fmt"

    log "github.com/cmelgarejo/go-gql-server/internal/logger"

    "github.com/cmelgarejo/go-gql-server/internal/orm/migration/jobs"
    "github.com/cmelgarejo/go-gql-server/internal/orm/models"
    "github.com/jinzhu/gorm"
    "gopkg.in/gormigrate.v1"
)

func updateMigration(db *gorm.DB) error {
    return db.AutoMigrate(
        &models.User{},
    ).Error
}

// ServiceAutoMigration migrates all the tables and modifications to the connected source
func ServiceAutoMigration(db *gorm.DB) error {
    // Keep a list of migrations here
    m := gormigrate.New(db, gormigrate.DefaultOptions, nil)
    m.InitSchema(func(db *gorm.DB) error {
        log.Info("[Migration.InitSchema] Initializing database schema")
        switch db.Dialect().GetName() {
        case "postgres":
            // Let's create the UUID extension, the user has to ahve superuser
            // permission for now
            db.Exec("create extension \"uuid-ossp\";")
        }
        if err := updateMigration(db); err != nil {
            return fmt.Errorf("[Migration.InitSchema]: %v", err)
        }
        // Add more jobs, etc here
        return nil
    })
    m.Migrate()

    if err := updateMigration(db); err != nil {
        return err
    }
    m = gormigrate.New(db, gormigrate.DefaultOptions, []*gormigrate.Migration{
        jobs.SeedUsers,
    })
    return m.Migrate()
} 
```

Enter fullscreen mode Exit fullscreen mode

现在首先我们看到几个更依赖于迁移的包，
`internal/orm/migration/jobs`和`internal/orm/models`

### 定义车型

让我们首先设置可以添加到`updateMigration`
func 中的模型，以便定义`user.go`但是首先，我对我的所有模型有一个特殊的需求，
我希望它们中的一些是软的*可删除的*(不从表本身删除)
一些可以完全销毁，因为我们使用的是 GORM 我们可以通过使用`gorm.Model` struct 来创建结构
，或者定义我们自己的结构*， 我
需要改变，还有一件事，`gorm.Model`也使用
自动递增的数字 id，但我喜欢把事情复杂化，所以我想
使用 UUID 的作为主键:D*

 *让我们用两个版本的
基础模型创建一个`internal/orm/models/base.go`模型文件，一个带有`hard`删除，另一个带有`soft`删除:

```
package models

import (
  "time"

  "github.com/gofrs/uuid"
  "github.com/jinzhu/gorm"
)

// BaseModel defines the common columns that all db structs should hold, usually
// db structs based on this have no soft delete
type BaseModel struct {
  // ID should use uuid_generate_v4() for the pk's
  ID        uuid.UUID  `gorm:"primary_key;type:uuid;default:uuid_generate_v4()"`
  CreatedAt time.Time  `gorm:"index;not null;default:CURRENT_TIMESTAMP"` // (My|Postgre)SQL
  UpdatedAt *time.Time `gorm:"index"`
}

// BaseModelSoftDelete defines the common columns that all db structs should
// hold, usually. This struct also defines the fields for GORM triggers to
// detect the entity should soft delete
type BaseModelSoftDelete struct {
  BaseModel
  DeletedAt *time.Time `sql:"index"`
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，有了这些基本模型，我们可以为我们的服务器定义我们需要的任何其他模型，
所以这里是`internal/orm/models/user.go` :

```
package models

// User defines a user for the app
type User struct {
  BaseModelSoftDelete         // We don't to actually delete the users, maybe audit if we want to hard delete them? or wait x days to purge from the table, also
  Email               string  `gorm:"not null;unique_index:idx_email"`
  UserID              *string // External user ID
  Name                *string
  NickName            *string
  FirstName           *string
  LastName            *string
  Location            *string `gorm:"size:1048"`
  Description         *string `gorm:"size:1048"`
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们将使用一个外部服务通过`Goth`
进行认证，并将其用于认证流，所以现在没有密码列。

好了，现在有了这个，我们只需要播种至少有一个用户的表，
我们将使用 [Gomigrate](https://gopkg.in/gormigrate.v1) pkg，并准备一个
迁移作业文件，让我们称它为:`internal/orm/migrations/jobs/seed_users.go`

```
package jobs

import (
    "github.com/cmelgarejo/go-gql-server/internal/orm/models"
    "github.com/jinzhu/gorm"
    "gopkg.in/gormigrate.v1"
)

var (
    uname                    = "Test User"
    fname                    = "Test"
    lname                    = "User"
    nname                    = "Foo Bar"
    description              = "This is the first user ever!"
    location                 = "His house, maybe? Wouldn't know"
    firstUser   *models.User = &models.User{
        Email:       "test@test.com",
        Name:        &uname,
        FirstName:   &fname,
        LastName:    &lname,
        NickName:    &nname,
        Description: &description,
        Location:    &location,
    }
)

// SeedUsers inserts the first users
var SeedUsers *gormigrate.Migration = &gormigrate.Migration{
    ID: "SEED_USERS",
    Migrate: func(db *gorm.DB) error {
        return db.Create(&firstUser).Error
    },
    Rollback: func(db *gorm.DB) error {
        return db.Delete(&firstUser).Error
    },
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经设置好了一切，我们可以修改出`cmd/gql-server/main.go`

```
package main

import (
  log "github.com/cmelgarejo/go-gql-server/internal/logger"

  "github.com/cmelgarejo/go-gql-server/internal/orm"
  "github.com/cmelgarejo/go-gql-server/pkg/server"
)

func main() {
  // Create a new ORM instance to send it to our
  orm, err := orm.Factory()
  if err != nil {
    log.Panic(err)
  }
  // Send: ORM instance
  server.Run(orm)
} 
```

Enter fullscreen mode Exit fullscreen mode

同样`pkg/server/main.go` :

```
package server

import (
  log "github.com/cmelgarejo/go-gql-server/internal/logger"

  "github.com/cmelgarejo/go-gql-server/internal/orm"

  "github.com/cmelgarejo/go-gql-server/internal/handlers"
  "github.com/cmelgarejo/go-gql-server/pkg/utils"
  "github.com/gin-gonic/gin"
)

var host, port, gqlPath, gqlPgPath string
var isPgEnabled bool

func init() {
  host = utils.MustGet("GQL_SERVER_HOST")
  port = utils.MustGet("GQL_SERVER_PORT")
  gqlPath = utils.MustGet("GQL_SERVER_GRAPHQL_PATH")
  gqlPgPath = utils.MustGet("GQL_SERVER_GRAPHQL_PLAYGROUND_PATH")
  isPgEnabled = utils.MustGetBool("GQL_SERVER_GRAPHQL_PLAYGROUND_ENABLED")
}

// Run spins up the server
func Run(orm *orm.ORM) {
  log.Info("GORM_CONNECTION_DSN: ", utils.MustGet("GORM_CONNECTION_DSN"))

  endpoint := "http://" + host + ":" + port

  r := gin.Default()
  // Handlers
  // Simple keep-alive/ping handler
  r.GET("/ping", handlers.Ping())

  // GraphQL handlers
  // Playground handler
  if isPgEnabled {
    r.GET(gqlPgPath, handlers.PlaygroundHandler(gqlPath))
    log.Info("GraphQL Playground @ " + endpoint + gqlPgPath)
  }
  // Pass in the ORM instance to the GraphqlHandler
  r.POST(gqlPath, handlers.GraphqlHandler(orm))
  log.Info("GraphQL @ " + endpoint + gqlPath)

  // Run the server
  // Inform the user where the server is listening
  log.Info("Running @ " + endpoint)
  // Print out and exit(1) to the OS if the server cannot run
  log.Fatal(r.Run(host + ":" + port))
} 
```

Enter fullscreen mode Exit fullscreen mode

GraphQL 处理程序也应该接收 ORM 实例，所以我们可以在解析器`internal/handlers/gql.go` :
中使用
数据库连接

```
package handlers

import (
  "github.com/99designs/gqlgen/handler"
  "github.com/cmelgarejo/go-gql-server/internal/gql"
  "github.com/cmelgarejo/go-gql-server/internal/gql/resolvers"
  "github.com/cmelgarejo/go-gql-server/internal/orm"
  "github.com/gin-gonic/gin"
)

// GraphqlHandler defines the GQLGen GraphQL server handler
func GraphqlHandler(orm *orm.ORM) gin.HandlerFunc {
  // NewExecutableSchema and Config are in the generated.go file
  c := gql.Config{
    Resolvers: &resolvers.Resolver{
      ORM: orm, // pass in the ORM instance in the resolvers to be used
    },
  }

  h := handler.GraphQL(gql.NewExecutableSchema(c))

  return func(c *gin.Context) {
    h.ServeHTTP(c.Writer, c.Request)
  }
}

// PlaygroundHandler defines a handler to expose the Playground
func PlaygroundHandler(path string) gin.HandlerFunc {
  h := handler.Playground("Go GraphQL Server", path)
  return func(c *gin.Context) {
    h.ServeHTTP(c.Writer, c.Request)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

至此，让我们修改
`Resolver`结构的`internal/gql/resolvers/main.go`以使用 ORM 实例:

```
package resolvers

import (
  "github.com/cmelgarejo/go-gql-server/internal/gql"
  "github.com/cmelgarejo/go-gql-server/internal/orm"
)

// Resolver is a modifiable struct that can be used to pass on properties used
// in the resolvers, such as DB access
type Resolver struct {
  ORM *orm.ORM
}

// Mutation exposes mutation methods
func (r *Resolver) Mutation() gql.MutationResolver {
  return &mutationResolver{r}
}

// Query exposes query methods
func (r *Resolver) Query() gql.QueryResolver {
  return &queryResolver{r}
}

type mutationResolver struct{ *Resolver }

type queryResolver struct{ *Resolver } 
```

Enter fullscreen mode Exit fullscreen mode

哦对了！让我们修改我们的`internal/schemas.graphql`来反映数据库
模型:

```
scalar Time
# Types
type User {
  id: ID!
  email: String!
  userId: String
  name: String
  firstName: String
  lastName: String
  nickName: String
  description: String
  location: String
  createdAt: Time!
  updatedAt: Time
}

# Input Types
input UserInput {
  email: String
  userId: String
  displayName: String
  name: String
  firstName: String
  lastName: String
  nickName: String
  description: String
  location: String
}

# List Types
type Users {
  count: Int # You want to return count for a grid for example
  list: [User!]! # that is why we need to specify the users object this way
}

# Define mutations here
type Mutation {
  createUser(input: UserInput!): User!
  updateUser(id: ID!, input: UserInput!): User!
  deleteUser(id: ID!): Boolean!
}

# Define queries here
type Query {
  users(id: ID): Users!
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能已经注意到了我返回`users`查询的方式。那是
,因为我们可能需要将记录的`count`返回到一个网格中，例如，
,我想在将来进行过滤和搜索，这就是`list`的用武之地

没有单一用户查询，这是为什么呢？我想`KISS`永远(不是你，
不要那样看着我！)这就是为什么我们将使用相同的查询来检索
单个特定记录或一组记录。

现在，让我们修改一下`scripts/gqlgen.sh`,选择在`internal/gql/resolvers/generated/resolver.go`重新生成
解析器函数，这样
就可以将任何新的查询或变异解析器复制到它自己的
文件中

```
#!/bin/bash
printf "\nRegenerating gqlgen files\n"
# Optional, delete the resolver to regenerate, only if there are new queries
# or mutations, if you are just changing the input or type definition and
# doesn't impact the resolvers definitions, no need to do it
while [[ "$#" -gt 0 ]]; do case $1 in
  -r|--resolvers)
    rm -f internal/gql/resolvers/generated/resolver.go
  ;;
  *) echo "Unknown parameter passed: $1"; exit 1;;
esac; shift; done time go run -v github.com/99designs/gqlgen
printf "\nDone.\n\n" 
```

Enter fullscreen mode Exit fullscreen mode

从`internal/gql/resolvers/generated/resolver.go`中取出需要的部分，有`"not implemented"`恐慌的
`func`，删除除`package resolvers`行之外的所有
，否则你会有影响`func`
的定义。

因此，最后我们可以为用户创建特定的解析器，这样就有了一个包含所有`internal/gql/resolvers/users.go` :
的新文件

```
package resolvers

import (
  "context"

  log "github.com/cmelgarejo/go-gql-server/internal/logger"

  "github.com/cmelgarejo/go-gql-server/internal/gql/models"
  tf "github.com/cmelgarejo/go-gql-server/internal/gql/resolvers/transformations"
  dbm "github.com/cmelgarejo/go-gql-server/internal/orm/models"
)

// CreateUser creates a record
func (r *mutationResolver) CreateUser(ctx context.Context, input models.UserInput) (*models.User, error) {
  return userCreateUpdate(r, input, false)
}

// UpdateUser updates a record
func (r *mutationResolver) UpdateUser(ctx context.Context, id string, input models.UserInput) (*models.User, error) {
  return userCreateUpdate(r, input, true, id)
}

// DeleteUser deletes a record
func (r *mutationResolver) DeleteUser(ctx context.Context, id string) (bool, error) {
  return userDelete(r, id)
}

// Users lists records
func (r *queryResolver) Users(ctx context.Context, id *string) (*models.Users, error) {
  return userList(r, id)
}

// ## Helper functions

func userCreateUpdate(r *mutationResolver, input models.UserInput, update bool, ids ...string) (*models.User, error) {
  dbo, err := tf.GQLInputUserToDBUser(&input, update, ids...)
  if err != nil {
    return nil, err
  }
  // Create scoped clean db interface
  db := r.ORM.DB.New().Begin()
  if !update {
    db = db.Create(dbo).First(dbo) // Create the user
  } else {
    db = db.Model(&dbo).Update(dbo).First(dbo) // Or update it
  }
  gql, err := tf.DBUserToGQLUser(dbo)
  if err != nil {
    db.RollbackUnlessCommitted()
    return nil, err
  }
  db = db.Commit()
  return gql, db.Error
}

func userDelete(r *mutationResolver, id string) (bool, error) {
  return false, nil
}

func userList(r *queryResolver, id *string) (*models.Users, error) {
  entity := "users"
  whereID := "id = ?"
  record := &models.Users{}
  dbRecords := []*dbm.User{}
  db := r.ORM.DB.New()
  if id != nil {
    db = db.Where(whereID, *id)
  }
  db = db.Find(&dbRecords).Count(&record.Count)
  for _, dbRec := range dbRecords {
    if rec, err := tf.DBUserToGQLUser(dbRec); err != nil {
      log.Errorfn(entity, err)
    } else {
      record.List = append(record.List, rec)
    }
  }
  return record, db.Error
} 
```

Enter fullscreen mode Exit fullscreen mode

您肯定想知道什么是`tf`包:还记得我在第 2 部分提到的`trasformations`
文件和文件夹吗？好了，这里是我们需要
*将*从 GQL 输入转换到数据库的地方`user` struct 容易保存到数据库中
并从数据库转换回
解析器的 GQL 返回 struct:

```
package transformations

import (
  "errors"

  gql "github.com/cmelgarejo/go-gql-server/internal/gql/models"
  dbm "github.com/cmelgarejo/go-gql-server/internal/orm/models"
  "github.com/gofrs/uuid"
)

// DBUserToGQLUser transforms [user] db input to gql type
func DBUserToGQLUser(i *dbm.User) (o *gql.User, err error) {
  o = &gql.User{
    ID:          i.ID.String(),
    Email:       i.Email,
    UserID:      i.UserID,
    Name:        i.Name,
    FirstName:   i.FirstName,
    LastName:    i.LastName,
    NickName:    i.NickName,
    Description: i.Description,
    Location:    i.Location,
    CreatedAt:   i.CreatedAt,
    UpdatedAt:   i.UpdatedAt,
  }
  return o, err
}

// GQLInputUserToDBUser transforms [user] gql input to db model
func GQLInputUserToDBUser(i *gql.UserInput, update bool, ids ...string) (o *dbm.User, err error) {
  o = &dbm.User{
    UserID:      i.UserID,
    Name:        i.Name,
    FirstName:   i.FirstName,
    LastName:    i.LastName,
    NickName:    i.NickName,
    Description: i.Description,
    Location:    i.Location,
  }
  if i.Email == nil && !update {
    return nil, errors.New("Field [email] is required")
  }
  if i.Email != nil {
    o.Email = *i.Email
  }
  if len(ids) > 0 {
    updID, err := uuid.FromString(ids[0])
    if err != nil {
      return nil, err
    }
    o.ID = updID
  }
  return o, err
} 
```

Enter fullscreen mode Exit fullscreen mode

回到`users.go`解析器，我已经将助手`func`从
解析器中分离出来，以使它们以最小和更可读的方式完成这项工作，将
解析器传递给助手函数将比在下一个[第 4 部分](//PART4.md)中仅仅传递
ORM 更有意义，那时我们将需要插件认证和
权限给解析器。

嘿，现在我们可以从数据库中查询和变异我们的用户了！

[![gql is showing db data!](img/f29fc55613a38de3e7e9289206c94b36.png "Hey there, sexy! (2)")](https://res.cloudinary.com/practicaldev/image/fetch/s--JUTipRP9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/cmelgarejo/go-gql-server/blob/tutorial/part-3/docs/tutoriimg/p3-p1.png%3Fraw%3Dtrue)

以及数据库中的特定用户:

[![gql is showing db data!](img/fddbd3dca6c33e7184ffe1cff7c7512e.png "Hey there, sexy! (3)")](https://res.cloudinary.com/practicaldev/image/fetch/s--me6pDuGb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/cmelgarejo/go-gql-server/blob/tutorial/part-3/docs/tutoriimg/p3-p2.png%3Fraw%3Dtrue)

我们总是可以创造一个新的:

[![gql is creating db data!](img/559a5ab61bdf44e0ed8d1a1059aaf762.png "Hey there, sexy! (4)")](https://res.cloudinary.com/practicaldev/image/fetch/s--_7IQ9JMV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/cmelgarejo/go-gql-server/blob/tutorial/part-3/docs/tutoriimg/p3-p3.png%3Fraw%3Dtrue)

并更新它:

[![gql is updating db data!](img/08d128b3d0a11079bde4a36da49ae3af.png "Hey there, sexy! (5)")](https://res.cloudinary.com/practicaldev/image/fetch/s--pYfwsO8Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/cmelgarejo/go-gql-server/blob/tutorial/part-3/docs/tutoriimg/p3-p4.png%3Fraw%3Dtrue)

好的，正如承诺的那样，接下来让我们通过添加几个包来增强我们的小项目。

## 附加:奖金部分

### 记录器包

包含的`"log"`包对我们来说已经足够了，但是我发现这个
结构良好的包叫做 [Logrus](http://github.com/sirupsen/logrus) 和
为它创建了一个包装器，在整个项目中使用，创建文件
`internal/logger/main.go` :

```
package logger

import (
  "github.com/sirupsen/logrus"
)

var logger *StandardLogger

func init() {
  logger = NewLogger()
}

// Event stores messages to log later, from our standard interface
type Event struct {
  id      int
  message string
}

// StandardLogger enforces specific log message formats
type StandardLogger struct {
  *logrus.Logger
}

// NewLogger initializes the standard logger
func NewLogger() *StandardLogger {
  var baseLogger = logrus.New()

  var standardLogger = &StandardLogger{baseLogger}

  standardLogger.Formatter = &logrus.TextFormatter{
    FullTimestamp: true,
  }
  // We could transform the errors into a JSON format, for external log SaaS tools such as splunk or logstash
  // standardLogger.Formatter = &logrus.JSONFormatter{
  //   PrettyPrint: true,
  // }

  return standardLogger
}

// Declare variables to store log messages as new Events
var (
  invalidArgMessage      = Event{1, "Invalid arg: %s"}
  invalidArgValueMessage = Event{2, "Invalid value for argument: %s: %v"}
  missingArgMessage      = Event{3, "Missing arg: %s"}
)

// Errorfn Log errors with format
func Errorfn(fn string, err error) {
  logger.Errorf("[%s]: %v", fn, err)
}

// InvalidArg is a standard error message
func InvalidArg(argumentName string) {
  logger.Errorf(invalidArgMessage.message, argumentName)
}

// InvalidArgValue is a standard error message
func InvalidArgValue(argumentName string, argumentValue string) {
  logger.Errorf(invalidArgValueMessage.message, argumentName, argumentValue)
}

// MissingArg is a standard error message
func MissingArg(argumentName string) {
  logger.Errorf(missingArgMessage.message, argumentName)
}

// Info Log
func Info(args ...interface{}) {
  logger.Infoln(args...)
}

// Infof Log
func Infof(format string, args ...interface{}) {
  logger.Infof(format, args...)
}

// Warn Log
func Warn(args ...interface{}) {
  logger.Warnln(args...)
}

// Warnf Log
func Warnf(format string, args ...interface{}) {
  logger.Warnf(format, args...)
}

// Panic Log
func Panic(args ...interface{}) {
  logger.Panicln(args...)
}

// Panicf Log
func Panicf(format string, args ...interface{}) {
  logger.Panicf(format, args...)
}

// Error Log
func Error(args ...interface{}) {
  logger.Errorln(args...)
}

// Errorf Log
func Errorf(format string, args ...interface{}) {
  logger.Errorf(format, args...)
}

// Fatal Log
func Fatal(args ...interface{}) {
  logger.Fatalln(args...)
}

// Fatalf Log
func Fatalf(format string, args ...interface{}) {
  logger.Fatalf(format, args...)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们有了一个具有良好特性的可扩展日志包

[![logger stuff](img/0a687cd4119b2d58a9d9935494dc8e42.png "Logging with Logrus")](https://res.cloudinary.com/practicaldev/image/fetch/s--2eOVn5EO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/cmelgarejo/go-gql-server/blob/tutorial/part-3/docs/tutoriimg/p3-p5.png%3Fraw%3Dtrue)

### 添加服务器换装实现

如果你对你所做的每一个改变都要重启你的服务器感到烦恼，那么我真的很想念`nodemon`，
幸运的是我们在 Go 中有更好的东西:

> `$ go get github.com/tockins/realize`

在这之后，你将在`$GOPATH/bin/realize`中拥有`realize`可执行文件以供使用，
因此，创建一个`.realize.yml`文件(或者在我们项目的根目录
中运行`$GOPATH/bin/realize init`，以交互方式创建它，这里是我的 *yml* 文件:

```
settings:
  files:
    outputs:
      status: false
      path: ''
      name: .r.outputs.log
    logs:
      status: false
      path: ''
      name: .r.logs.log
    errors:
      status: false
      path: ''
      name: .r.errors.log
  legacy:
    force: false
    interval: 0s
schema:
  - name: gql-server
    path: cmd/gql-server
    commands:
      install:
        status: false
      run:
        status: true
    watcher:
      extensions:
        - go
      paths:
        - ../../
      ignore:
        paths:
          - .git
          - .realize
          - .vscode
          - vendor 
```

Enter fullscreen mode Exit fullscreen mode

这应该足够运行我们下一步要做的脚本了，将
`scripts/run.sh`复制到`scripts/dev-run.sh`并修改如下:

```
#!/bin/sh
app="gql-server"
src="$srcPath/$app/$pkgFile"

printf "\nStart running: $app\n"
# Set all ENV vars for the server to run
export $(grep -v '^#' .env | xargs)
time /$GOPATH/bin/realize start run
# This should unset all the ENV vars, just in case.
unset $(grep -v '^#' .env | sed -E 's/(.*)=.*/\1/' | xargs)
printf "\nStopped running: $app\n\n" 
```

Enter fullscreen mode Exit fullscreen mode

另外，将`run.sh`改为:

```
#!/bin/sh
buildPath="build"
app="gql-server"
program="$buildPath/$app"

printf "\nStart app: $app\n"
# Set all ENV vars for the program to run
export $(grep -v '^#' .env | xargs)
time ./$program
# This should unset all the ENV vars, just in case.
unset $(grep -v '^#' .env | sed -E 's/(.*)=.*/\1/' | xargs)
printf "\nStopped app: $app\n\n" 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在你可以用`.scripts/dev-run.sh`运行我们的项目，并在你每次修改 go 文件时让它重新加载
！整洁，对不对？

同样，和前面的部分一样，所有的代码都可以在的
[库中找到！如果你有问题，批评和意见，让我们一起学习更多！](https://github.com/cmelgarejo/go-gql-server/tree/tutorial/part-3)

和...

> 很抱歉这篇冗长的帖子，这里有一个地鼠土豆: [![gopher potato](img/3a8e041bb47f4d0770772ac2798dc3a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z1oKdN7u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://golang.org/doc/gopher/gophercolor.png)*
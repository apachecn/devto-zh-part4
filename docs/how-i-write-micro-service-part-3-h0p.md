# 我如何写微服务(第三部分)

> 原文：<https://dev.to/zenithar/how-i-write-micro-service-part-3-h0p>

`Repositories`按照六边形架构的定义，这个组件是一个`persisence adapter`。这是一个`models`提供者的技术实现。可能是:

*   本地提供者:数据库、文件；
*   远程提供者:另一种微服务。

我习惯于根据所使用的技术后端将存储库实现拆分到一个专用的包中。

```
$ mkdir internal/repositories
$ touch internal/repositories/api.go
$ mkdir internal/repositories/pkg
$ mkdir internal/repositories/pkg/postgresql
$ mkdir internal/repositories/pkg/mongodb 
```

文件系统应该如下所示:

```
 + internal
   + helpers
     - id.go
     - password.go
     - time.go
   + models
     - user.go
   + repositories
     - api.go
     + pkg
       + postgresql
       + mongodb 
```

# 适配器合同

但是在开始实现`persistence adapter`之前，为了符合`The Clean Architecture`的`dependency loose coupling`，我们必须先声明适配器接口。因此所有实现都必须符合。

例如`api.go`，必须只包含`repository contract`。

```
package repositories

import (
    "context"

    "go.zenithar.org/pkg/db"
    "go.zenithar.org/spotigraph/internal/models"
)

// UserSearchFilter represents user entity collection search criteria
type UserSearchFilter struct {
    UserID    string
    // Principal hash value must be used
    Principal string
}

// User describes user repository contract
type User interface {
    // Create a user
    Create(ctx context.Context, entity *models.User) error
    // Retrieve an user
    Get(ctx context.Context, id string) (*models.User, error)
    // Update selectively an user instance
    Update(ctx context.Context, entity *models.User) error
    // Delete an user instance
    Delete(ctx context.Context, id string) error
    // Search for user entities using filter, pagination and sorts
    Search(ctx context.Context, filter *UserSearchFilter, p *db.Pagination, s *db.SortParameters) ([]*models.User, int, error)
    // Retrieve an user by principal hash value
    FindByPrincipal(ctx context.Context, principal string) (*models.User, error)
} 
```

> 不要忘记在调用中尽可能传递`context.Context`，以防请求取消绑定到 HTTP 请求。
> 当调用者取消 HTTP 请求时，SQL 请求也必须被取消，等等。

我们的存储库实现必须处理实体，因此在使用之前必须对`principal`进行哈希处理(例如`FindByPrincipal`)

## 单元测试

为了测试，我在`api.go`中添加了这个 go 编译器生成步骤，从接口类型定义构建`User`模拟。

```
//go:generate mockgen -destination test/mock/user.gen.go -package mock go.zenithar.org/spotigraph/internal/repositories User 
```

> 服务单元测试将使用存储库模拟。

## 集成测试

这些测试是使用真实的后端执行的。

例如，`User`集成测试生成器创建了一个完整的测试，使用`persistence adapter`实现来创建、读取、更新、删除`User`。

> 使用构建标签`integration`
> 将`Integration tests`从默认测试目标中逐出

```
//+build integration

package specs

import (
    "context"
    "testing"

    "github.com/google/go-cmp/cmp"
    . "github.com/onsi/gomega"

    "go.zenithar.org/pkg/db"
    "go.zenithar.org/spotigraph/internal/models"
    "go.zenithar.org/spotigraph/internal/repositories"
)

// User returns user repositories full test scenario builder
func User(underTest repositories.User) func(*testing.T) {
    return func(t *testing.T) {
        // Flag this test that could be run in parallel
        t.Parallel()

        // Prepare gomega matcher
        g:= NewGomegaWithT(t)

        // Stub context
        ctx:= context.Background()

        // Prepare a new entity
        created:= models.NewUser("toto@foo.com")
        g.Expect(created).ToNot(BeNil(), "Newly created entity should not be nil")

        // Create the entity using repository
        err:= underTest.Create(ctx, created)
        g.Expect(err).To(BeNil(), "Error creation should be nil")

        // -------------------------------------------------------------------------

        // Retrieve by id from repository
        saved, err:= underTest.Get(ctx, created.ID)
        g.Expect(err).To(BeNil(), "Retrieval error should be nil")
        g.Expect(saved).ToNot(BeNil(), "Saved entity should not be nil")

        // Compare objects
        g.Expect(cmp.Equal(created, saved)).To(BeTrue(), "Saved and Created should be equals")

        // Retrieve by non-existent id
        nonExistent, err:= underTest.Get(ctx, "non-existent-id")
        g.Expect(err).ToNot(BeNil(), "Error should be raised on non-existent entity")
        g.Expect(err).To(Equal(db.ErrNoResult), "Error ErrNoResult should be raised")
        g.Expect(nonExistent).To(BeNil(), "Non-existent entity should be nil")

        // Retrieve by principal
        savedPrincipal, err:= underTest.FindByPrincipal(ctx, created.Principal)
        g.Expect(err).To(BeNil(), "Retrieval error should be nil")
        g.Expect(savedPrincipal).ToNot(BeNil(), "Saved entity should not be nil")

        // Compare objects
        g.Expect(cmp.Equal(created, savedPrincipal)).To(BeTrue(), "SavedPrincipal and Created should be equals")

        // -------------------------------------------------------------------------

        // Update an entity
        saved, err = underTest.Get(ctx, created.ID)
        g.Expect(err).To(BeNil(), "Retrieval error should be nil")
        g.Expect(saved).ToNot(BeNil(), "Saved entity should not be nil")

        // Update properties

        // Update with repository
        err = underTest.Update(ctx, saved)
        g.Expect(err).To(BeNil(), "Update error should be nil")

        // Retrieve from repository to check updated properties
        updated, err:= underTest.Get(ctx, created.ID)
        g.Expect(err).To(BeNil(), "Retrieval error should be nil")
        g.Expect(updated).ToNot(BeNil(), "Saved entity should not be nil")

        // Compare objects
        g.Expect(cmp.Equal(created, updated)).To(BeTrue(), "Saved and Updated should be equals")

        // -------------------------------------------------------------------------

        // Remove an entity
        err = underTest.Delete(ctx, created.ID)
        g.Expect(err).To(BeNil(), "Removal error should be nil")

        // Retrieve from repository to check deletion
        deleted, err:= underTest.Get(ctx, created.ID)
        g.Expect(err).ToNot(BeNil(), "Deletion error should not be nil")
        g.Expect(err).To(Equal(db.ErrNoResult), "Error ErrNoResult should be raised")
        g.Expect(deleted).To(BeNil(), "Deleted entity should be nil")

        // Remove a non-existent entity
        err = underTest.Delete(ctx, "non-existent-id")
        g.Expect(err).ToNot(BeNil(), "Removal error should not be nil")
        g.Expect(err).To(Equal(db.ErrNoModification), "Error ErrNoModification should be raised")
    }
} 
```

# 持久性实现

多年来，我已经改进、重建了大约 30 次我自己的框架。所有的实现都以它为基础，你可以自由地从中提取你需要的东西。

> [go.zenithar.org/pkg](https://github.com/Zenithar/go-pkg)

它是[多子模块 Golang 库](https://github.com/golang/go/wiki/Modules)，所以你不需要拉整个库，所有的东西都在一个最小的包中被
分离和独立版本化。

## PostgreSQL

首先，我们需要准备 PostgreSQL 包。

```
$ mkdir internal/repositories/pkg/postgresql/migrations
$ touch internal/repositories/pkg/postgresql/setup.go
$ touch internal/repositories/pkg/postgresql/table_user.go 
```

### 模式迁移处理

我习惯于将所有数据库特定的准备设置放在一个名为`setup.go`的文件中。
这用于声明所有的表名常量，以及使用`packr`嵌入到最终
atifact 中的数据库迁移。

> 为什么是`packr`？高浓缩铀，因为...不是`go-bin-data`...？

```
package postgresql

import (
    "github.com/gobuffalo/packr"
    "github.com/google/wire"
    "github.com/jmoiron/sqlx"
    "golang.org/x/xerrors"

    // Load postgresql drivers
    _ "github.com/jackc/pgx"
    _ "github.com/jackc/pgx/pgtype"
    _ "github.com/jackc/pgx/stdlib"
    _ "github.com/lib/pq"

    migrate "github.com/rubenv/sql-migrate"
    db "go.zenithar.org/pkg/db/adapter/postgresql"
)

// ----------------------------------------------------------

var (
    // UserTableName represents users collection name
    UserTableName = "users"
)

// ----------------------------------------------------------

//go:generate packr

// migrations contains all schema migrations
var migrations = &migrate.PackrMigrationSource{
    Box: packr.NewBox("./migrations"),
}

// CreateSchemas create or updates the current database schema
func CreateSchemas(conn *sqlx.DB) (int, error) {
    // Migrate schema
    migrate.SetTable("schema_migration")

    // Run the schema migration from embedded resource
    n, err := migrate.Exec(conn.DB, conn.DriverName(), migrations, migrate.Up)
    if err != nil {
        return 0, xerrors.Errorf("postgresql: could not migrate sql schema, applied %d migrations :%w", n, err)
    }

    return n, nil
} 
```

数据库模式迁移如下:

```
-- +migrate Up
CREATE TABLE IF NOT EXISTS chapters (
    id          VARCHAR(32) NOT NULL PRIMARY KEY,
    name        VARCHAR(50) NOT NULL,
    meta        JSON        NOT NULL,
    leader_id   VARCHAR(32) NOT NULL,
    member_ids  JSON        NOT NULL
);

-- +migrate Down
DROP TABLE chapters; 
```

迁移方向有 2 个“注释”,您的迁移工具使用它来根据升级或降级过程应用正确的
SQL 脚本。

> 细心的人应该注意到我加载了两个不同的 PostgreSQL 驱动程序(`pq`和`pgx`)。

内部数据库迁移的规则是:

*   仅对集成测试使用自动迁移
*   不要试图在服务启动时自动应用迁移，如果是集群，运行的代码将与更新的持久性模式不兼容
*   知道你在做什么

### 用户持久性适配器

例如作为`internal/repositories/pkg/postgresql`
中`table_user.go`中`PostgreSQL`的`User`持久性适配器

```
package postgresql

import (
    "context"
    "strings"

    "go.zenithar.org/pkg/db"
    "go.zenithar.org/pkg/db/adapter/postgresql"
    "go.zenithar.org/spotigraph/internal/models"
    "go.zenithar.org/spotigraph/internal/repositories"

    sq "github.com/Masterminds/squirrel"
    "github.com/jmoiron/sqlx"
    "golang.org/x/xerrors"
)

type pgUserRepository struct {
    adapter *postgresql.Default
}

// NewUserRepository returns a Postgresql user management repository instance
func NewUserRepository(session *sqlx.DB) repositories.User {
    // Default columns to retrieve
    defaultColumns := []string{"user_id", "principal", "secret", "creation_date"}

    // Sortable columns for criteria
    sortableColumns := []string{"user_id", "principal", "creation_date"}

    // Initialize repository
    return &pgUserRepository{
        adapter: postgresql.NewCRUDTable(session, "", UserTableName, defaultColumns, sortableColumns),
    }
}

// -----------------------------------------------------------------------------

func (r *pgUserRepository) Create(ctx context.Context, entity *models.User) error {
    // Validate entity first
    if err := entity.Validate(); err != nil {
        return xerrors.Errorf("postgresql: unable to validate entity, creation aborted : %w", err)
    }

    return r.adapter.Create(ctx, entity)
}

func (r *pgUserRepository) Get(ctx context.Context, realmID, id string) (*models.User, error) {
    var entity models.User

    // Delegate to my own postgresql adpater
    if err := r.adapter.WhereAndFetchOne(ctx, map[string]interface{}{
        "user_id":  id,
    }, &entity); err != nil {
        return nil, xerrors.Errorf("postgresql: unable to query database: %w", err)
    }

    return &entity, nil
}

func (r *pgUserRepository) Update(ctx context.Context, entity *models.User) error {
    // Validate entity first
    if err := entity.Validate(); err != nil {
        return xerrors.Errorf("postgresql: unable to validate entity, update aborted : %w", err)
    }

    // Only update allowed attributes, never, never, did I say never ?
    // always the full object.
    if err := r.adapter.Update(ctx, map[string]interface{}{
        "secret": entity.Secret,
    }, map[string]interface{}{
        "user_id":  entity.ID,
    }); err != nil {
        return xerrors.Errorf("postgresql: unable to update entity: %w", err)
    }

    // No error
    return nil
}

func (r *pgUserRepository) Delete(ctx context.Context, realmID, id string) error {

    // Delegate to adapter
    if err := r.adapter.RemoveOne(ctx, map[string]interface{}{
        "user_id":  id,
    }); err != nil {
        return xerrors.Errorf("postgresql: unable to remove entity: %w", err)
    }

    // No error
    return nil
}

func (r *pgUserRepository) Search(ctx context.Context, filter *repositories.UserSearchFilter, pagination *db.Pagination, sortParams *db.SortParameters) ([]*models.User, int, error) {
    var results []*models.User

    // Delegate to adapter
    count, err := r.adapter.Search(ctx, r.buildFilter(filter), pagination, sortParams, &results)
    if err != nil {
        return nil, count, xerrors.Errorf("postgresql: unable to list entities: %w", err)
    }

    // Standardize the error on no result to be coherent with all implementations
    if len(results) == 0 {
        return results, count, db.ErrNoResult
    }

    // Return results and total count
    return results, count, nil
}

func (r *pgUserRepository) FindByPrincipal(ctx context.Context, realmID string, principal string) (*models.User, error) {
    var entity models.User

    // Delegate to adapter
    if err := r.adapter.WhereAndFetchOne(ctx, map[string]interface{}{
        "principal": principal,
    }, &entity); err != nil {
        return nil, xerrors.Errorf("postgresql: unable to retrieve entity: %w", err)
    }

    return &entity, nil
}

// -----------------------------------------------------------------------------

func (r *pgUserRepository) buildFilter(filter *repositories.UserSearchFilter) interface{} {
    if filter != nil {
        clauses := sq.Eq{
            "1": "1",
        }

        if len(strings.TrimSpace(filter.UserID)) > 0 {
            clauses["user_id"] = filter.UserID
        }
        if len(strings.TrimSpace(filter.Principal)) > 0 {
            clauses["principal"] = filter.Principal
        }

        return clauses
    }

    return nil
} 
```

### 小队持续适配器

如果`model`不能直接放入持久性适配器，你必须根据需要转换它

使用给定的表模式:

```
-- +migrate Up
CREATE TABLE IF NOT EXISTS squads (
    id                  VARCHAR(32) NOT NULL PRIMARY KEY,
    name                VARCHAR(50) NOT NULL,
    meta                JSON        NOT NULL,
    product_owner_id    VARCHAR(32) NOT NULL,
    member_ids          JSON        NOT NULL
);

-- +migrate Down
DROP TABLE squads; 
```

> 在这种情况下，我选择使用`JSON`列类型来处理`Squad:members`关联和`Squad:metadata`。所以我必须在写入数据库之前将`member_ids`数组转换为 JSON 对象，解码时从 JSON 读取到一个数组。

我的持久性适配器必须在每个 SQL 操作之前转换模型。

```
package postgresql

import (
    "context"
    "encoding/json"
    "strings"

    api "go.zenithar.org/pkg/db"
    db "go.zenithar.org/pkg/db/adapter/postgresql"
    "go.zenithar.org/spotigraph/internal/models"
    "go.zenithar.org/spotigraph/internal/repositories"

    sq "github.com/Masterminds/squirrel"
    "github.com/jmoiron/sqlx"
    "github.com/pkg/errors"
)

type pgSquadRepository struct {
    adapter *db.Default
}

// NewSquadRepository returns an initialized PostgreSQL repository for squads
func NewSquadRepository(cfg *db.Configuration, session *sqlx.DB) repositories.Squad {
    // Defines allowed columns
    defaultColumns := []string{
        "id", "name", "meta", "product_owner_id", "member_ids",
    }

    // Sortable columns
    sortableColumns := []string{
        "name", "product_owner_id",
    }

    return &pgSquadRepository{
        adapter: db.NewCRUDTable(session, "", SquadTableName, defaultColumns, sortableColumns),
    }
}

// ------------------------------------------------------------

type sqlSquad struct {
    ID             string `db:"id"`
    Name           string `db:"name"`
    Meta           string `db:"meta"`
    ProductOwnerID string `db:"product_owner_id"`
    MemberIDs      string `db:"member_ids"`
}

func toSquadSQL(entity *models.Squad) (*sqlSquad, error) {
    meta, err := json.Marshal(entity.Meta)
    if err != nil {
        return nil, errors.WithStack(err)
    }

    members, err := json.Marshal(entity.MemberIDs)
    if err != nil {
        return nil, errors.WithStack(err)
    }

    return &sqlSquad{
        ID:             entity.ID,
        Name:           entity.Name,
        Meta:           string(meta),
        MemberIDs:      string(members),
        ProductOwnerID: entity.ProductOwnerID,
    }, nil
}

func (dto *sqlSquad) ToEntity() (*models.Squad, error) {
    entity := &models.Squad{
        ID:             dto.ID,
        Name:           dto.Name,
        ProductOwnerID: dto.ProductOwnerID,
    }

    // Decode JSON columns

    // Metadata
    err := json.Unmarshal([]byte(dto.Meta), &entity.Meta)
    if err != nil {
        return nil, errors.WithStack(err)
    }

    // Membership
    err = json.Unmarshal([]byte(dto.MemberIDs), &entity.MemberIDs)
    if err != nil {
        return nil, errors.WithStack(err)
    }

    return entity, nil
}

// ------------------------------------------------------------

func (r *pgSquadRepository) Create(ctx context.Context, entity *models.Squad) error {
    // Validate entity first
    if err := entity.Validate(); err != nil {
        return err
    }

    // Convert to DTO
    data, err := toSquadSQL(entity)
    if err != nil {
        return err
    }

    return r.adapter.Create(ctx, data)
}

func (r *pgSquadRepository) Get(ctx context.Context, id string) (*models.Squad, error) {
    var entity sqlSquad

    if err := r.adapter.WhereAndFetchOne(ctx, map[string]interface{}{
        "id": id,
    }, &entity); err != nil {
        return nil, err
    }

    return entity.ToEntity()
}

func (r *pgSquadRepository) Update(ctx context.Context, entity *models.Squad) error {
    // Validate entity first
    if err := entity.Validate(); err != nil {
        return err
    }

    // Intermediary DTO
    obj, err := toSquadSQL(entity)
    if err != nil {
        return err
    }

    return r.adapter.Update(ctx, map[string]interface{}{
        "name":             obj.Name,
        "meta":             obj.Meta,
        "product_owner_id": obj.ProductOwnerID,
        "member_ids":       obj.MemberIDs,
    }, map[string]interface{}{
        "id": entity.ID,
    })
} 
```

## MongoDB

另一个带`NoSQL persistence adapter`的例子。

```
package mongodb

import (
    "context"

    mongowrapper "github.com/opencensus-integrations/gomongowrapper"
    api "go.zenithar.org/pkg/db"
    db "go.zenithar.org/pkg/db/adapter/mongodb"
    "go.zenithar.org/spotigraph/internal/models"
    "go.zenithar.org/spotigraph/internal/repositories"
)

type mgoSquadRepository struct {
    adapter *db.Default
}

// NewSquadRepository returns an initialized MongoDB repository for squads
func NewSquadRepository(cfg *db.Configuration, session *mongowrapper.WrappedClient) repositories.Squad {
    return &mgoSquadRepository{
        adapter: db.NewCRUDTable(session, cfg.DatabaseName, SquadTableName),
    }
}

// ------------------------------------------------------------

func (r *mgoSquadRepository) Create(ctx context.Context, entity *models.Squad) error {
    // Validate entity first
    if err := entity.Validate(); err != nil {
        return err
    }

    return r.adapter.Insert(ctx, entity)
}

func (r *mgoSquadRepository) Get(ctx context.Context, id string) (*models.Squad, error) {
    var entity models.Squad

    if err := r.adapter.WhereAndFetchOne(ctx, map[string]interface{}{
        "id": id,
    }, &entity); err != nil {
        return nil, err
    }

    return &entity, nil
}

func (r *mgoSquadRepository) Update(ctx context.Context, entity *models.Squad) error {
    // Validate entity first
    if err := entity.Validate(); err != nil {
        return err
    }

    return r.adapter.Update(ctx, map[string]interface{}{
        "name":             entity.Name,
        "meta":             entity.Meta,
        "product_owner_id": entity.ProductOwnerID,
    }, map[string]interface{}{
        "id": entity.ID,
    })
}

func (r *mgoSquadRepository) Delete(ctx context.Context, id string) error {
    return r.adapter.Delete(ctx, id)
}

func (r *mgoSquadRepository) FindByName(ctx context.Context, name string) (*models.Squad, error) {
    var entity models.Squad

    if err := r.adapter.WhereAndFetchOne(ctx, map[string]interface{}{
        "name": name,
    }, &entity); err != nil {
        return nil, err
    }

    return &entity, nil
}

func (r *mgoSquadRepository) AddMembers(ctx context.Context, id string, users ...*models.User) error {
    // Retrieve squad entity
    entity, err := r.Get(ctx, id)
    if err != nil {
        return err
    }

    // Add user as members
    for _, u := range users {
        entity.AddMember(u)
    }

    // Update members only
    return r.adapter.Update(ctx, map[string]interface{}{
        "member_ids": entity.MemberIDs,
    }, map[string]interface{}{
        "id": entity.ID,
    })
}

func (r *mgoSquadRepository) RemoveMembers(ctx context.Context, id string, users ...*models.User) error {
    // Retrieve squad entity
    entity, err := r.Get(ctx, id)
    if err != nil {
        return err
    }

    // Remove user from members
    for _, u := range users {
        entity.RemoveMember(u)
    }

    // Update members
    return r.adapter.Update(ctx, map[string]interface{}{
        "member_ids": entity.MemberIDs,
    }, map[string]interface{}{
        "id": entity.ID,
    })
} 
```

> 在没有控制每个键的情况下，不要更新一个完整的对象，你必须设置更新功能来只更新`updatable`属性。

## 远程适配器

您的适配器可以是一个外部服务，它提供数据并通过传输协议(HTTP、gRPC 等)调用。)

> Mocks 可以用来在测试期间模拟远程访问。

# 运行集成测试

为了用 Golang 运行集成测试，您必须准备一个`TestMain`。这个 runner 负责根据请求的命令行标志构建所有相关的`persistence adapter instance`。

通过将持久性适配器传递给生成器，测试规范将用于生成完整的场景测试。

> 显然，所有持久性适配器实现都应该具有相同的行为，并由您的测试套件进行验证。

```
// +build integration

package integration

import (
    "context"
    "flag"
    "fmt"
    "math/rand"
    "os"
    "strings"
    "testing"
    "time"

    "go.uber.org/zap"
    "go.zenithar.org/pkg/log"
    "go.zenithar.org/pkg/testing/containers/database"
    "go.zenithar.org/spotigraph/internal/version"
)

var databases = flag.String("databases", "postgresql", "Repositories backend to use, splitted with a coma ','. Example: postgresql,mongodb,rethinkdb")

func init() {
    flag.Parse()

    ctx := context.Background()

    // Prepare logger
    log.Setup(ctx, &log.Options{
        Debug:     true,
        AppName:   "spotigraph-integration-tests",
        AppID:     "123456",
        Version:   version.Version,
        Revision:  version.Revision,
        SentryDSN: "",
    })

    // Initialize random seed
    rand.Seed(time.Now().UTC().Unix())

    // Set UTC for all time
    time.Local = time.UTC
}

func testMainWrapper(m *testing.M) int {
    if testing.Short() {
        fmt.Println("Skipping integration tests")
        return 0
    }

    log.Bg().Info("Initializing test DB for integration test (disable with `go test -short`)")

    ctx := context.Background()
    backends := strings.Split(strings.ToLower(*databases), ",")

    for _, back := range backends {
        switch back {
        case "postgresql":
            // Initialize postgresql
            cancel, err := postgreSQLConnection(ctx)
            if err != nil {
                log.Bg().Fatal("Unable to initialize repositories", zap.Error(err))
            }
            defer func() {
                cancel()
            }()
        default:
            log.Bg().Fatal("Unsupported backend", zap.String("backend", back))
        }
    }

    defer func() {
        database.KillAll(ctx)
    }()

    return m.Run()
}

// TestMain is the test entrypoint
func TestMain(m *testing.M) {
    os.Exit(testMainWrapper(m))
} 
```

它将使用`ory-am/dockertest`初始化运行 PostgreSQL 服务器的 Docker 容器。当在本地执行测试时，它将自动执行数据库服务器部署，也可以在远程数据库实例上使用(例如，当使用您的 CI 管道管理数据库执行时)。

```
package database

import (
    "fmt"
    "log"

    // Load driver if not already done
    _ "github.com/lib/pq"

    "github.com/dchest/uniuri"
    dockertest "gopkg.in/ory-am/dockertest.v3"

    "go.zenithar.org/pkg/testing/containers"
)

var (
    // PostgreSQLVersion defines version to use
    PostgreSQLVersion = "10"
)

// PostgreSQLContainer represents database container handler
type postgreSQLContainer struct {
    Name     string
    pool     *dockertest.Pool
    resource *dockertest.Resource
    config   *Configuration
}

// NewPostgresContainer initialize a PostgreSQL server in a docker container
func newPostgresContainer(pool *dockertest.Pool) *postgreSQLContainer {

    var (
        databaseName = fmt.Sprintf("test-%s", uniuri.NewLen(8))
        databaseUser = fmt.Sprintf("user-%s", uniuri.NewLen(8))
        password     = uniuri.NewLen(32)
    )

    // Initialize a PostgreSQL server
    resource, err := pool.Run("postgres", PostgreSQLVersion, []string{
        fmt.Sprintf("POSTGRES_PASSWORD=%s", password),
        fmt.Sprintf("POSTGRES_DB=%s", databaseName),
        fmt.Sprintf("POSTGRES_USER=%s", databaseUser),
    })
    if err != nil {
        log.Fatalf("Could not start resource: %s", err)
    }

    // Prepare connection string
    connectionString := fmt.Sprintf("postgres://%s:%s@localhost:%s/%s?sslmode=disable", databaseUser, password, resource.GetPort("5432/tcp"), databaseName)

    // Retrieve container name
    containerName := containers.GetName(resource)

    // Return container information
    return &postgreSQLContainer{
        Name:     containerName,
        pool:     pool,
        resource: resource,
        config: &Configuration{
            ConnectionString: connectionString,
            Password:         password,
            DatabaseName:     databaseName,
            DatabaseUser:     databaseUser,
        },
    }
}

// -------------------------------------------------------------------

// Close the container
func (container *postgreSQLContainer) Close() error {
    log.Printf("Postgres (%v): shutting down", container.Name)
    return container.pool.Purge(container.resource)
}

// Configuration return database settings
func (container *postgreSQLContainer) Configuration() *Configuration {
    return container.config
} 
```

尝试通过运行迁移来连接和构建数据库模式的连接构建器。

```
// +build integration

package integration

import (
    "context"

    "github.com/pkg/errors"
    "go.uber.org/zap"

    "go.zenithar.org/pkg/log"
    "go.zenithar.org/pkg/testing/containers/database"
    "go.zenithar.org/spotigraph/internal/repositories/pkg/postgresql"
)

func postgreSQLConnection(ctx context.Context) (func(), error) {
    // Initialize connection and/or container
    conn, _, err := database.ConnectToPostgreSQL(ctx)
    if err != nil {
        return nil, errors.Wrap(err, "unable to initialize database server")
    }

    // Try to contact server
    if err = conn.Ping(); err != nil {
        return nil, errors.Wrap(err, "unable to contact database")
    }

    // Migrate schema
    n, err := postgresql.CreateSchemas(conn)
    if err != nil {
        return nil, errors.Wrap(err, "unable to initialize database schema")
    }

    // Log migration
    log.For(ctx).Info("Applyied migrations to database", zap.Int("level", n))

    // Build repositories
    userRepositories["postgresql"] = postgresql.NewUserRepository(nil, conn)

    // Return result
    return func() {
        log.SafeClose(conn, "unable to close connection")
    }, nil
} 
```

> 完整的集成测试套件可以在这里找到-[https://github . com/zenith ar/go-spot igraph/blob/master/internal/repositories/test/integration/main _ test . go](https://github.com/Zenithar/go-spotigraph/blob/master/internal/repositories/test/integration/main_test.go)

# 结论

此时，您必须能够像`persistence adapters`一样`execute`您的域上的所有测试套件。您应该能够通过接口使用这些`adapters`来创建业务服务，而不是直接通过适配器实例。所有持久性适配器都受`api.go`中定义的契约约束，从公共端来看，所有适配器用户都必须适应并坚持这个接口，以确保在`persistence`层中有`port`和`interface`。通过这样做，你将符合六边形建筑原则。

在下一篇文章中，我们将通过声明调度程序用来与业务服务通信的协议和契约来开始准备服务。

## 参考文献

*   [Github Spotigraph](https://github.com/Zenithar/go-spotigraph)
*   [PostgreSQL PGX 驱动程序](https://github.com/jackc/pgx)
*   [PostgreSQL 查询生成器](https://github.com/Masterminds/squirrel)
# Vapor 指南——使用 Heroku 和 Ubuntu 设置和部署

> 原文：<https://dev.to/leogdion/a-vapor-guide-setup-and-deployment-with-heroku-and-ubuntu-49jn>

为你的移动应用选择合适的后端可能是一个真正的挑战。例如，Swift 开发人员没有任何东西不需要在另一种语言上运行。因此，我决定给[做一个关于 Vapor](https://learningswift.brightdigit.com/vapor-swift-backend-review/?utm_medium=web&utm_source=learning-swift) 的回顾，看看这是否真的是一个正确的选择。

**我发现对于 Swift 开发者来说， [Vapor](https://vapor.codes/) 是一个非常好的选择，并且很容易与**一起工作。此外，如果你的开发团队致力于 Vapor，Vapor 有一个有效的社区和一个很棒的 API。因此，不要让你的团队从 Xcode 和 Swift 跳到 VSCode 或 Firebase 等…

在本文中，我将介绍如何开始，具体来说:

*   **为 Vapor 开发设置您的 Mac**
*   使用 PostgreSQL 数据库将您的项目转换为
***   为 Heroku 部署修改您的项目*   为 Vapor 准备一个 **Ubuntu 服务器***   为 **Ubuntu 部署**修改您的项目**

 **因此，您将拥有一个完全可用的 Postgres 后端，并且易于部署。

### 为 Vapor 开发设置 Mac

为了开始开发 Vapor 应用程序，我们需要设置我们的 Mac。根据 Vapor 文档，**您需要 Xcode 9.3 或更高版本**。然而对于本教程，我在写作时使用了最新版本的 macOS 和 Xcode:

### 蒸汽的先决条件

我还安装了[自制软件](https://brew.sh/)。要安装家酿软件，打开你的终端应用程序，输入:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` 
```

更多细节你可以去官方自制主页。

### 安装蒸汽

一旦你安装了自制软件，我们就可以在你的 Mac 上安装 **Vapor** 。在我们继续之前，我们需要将 Vapor 存储库添加到 Homebrew 中。

[https://www.youtube.com/embed/yYCxa6ev1Ng](https://www.youtube.com/embed/yYCxa6ev1Ng)

1.  首先，**使用`tap`命令:`brew tap vapor/tap`添加蒸汽库**
2.  接下来，**安装 Vapor 工具包** : `brew install vapor/tap/vapor`由于安装了 Vapor 工具包，**我们将可以访问 Vapor CLI 工具**。因此，您可以通过键入:`vapor --help`来访问可用命令列表

例如，**使用 Vapor CLI 工具**，您可以:

*   **构建**应用程序
*   **运行**应用
*   稍后为 **Heroku** 部署设置
*   创建 **Xcode** 项目，当然还有…
*   创建**新的** Swift 包

### 创建您的第一个 Vapor 项目

[https://www.youtube.com/embed/iZqHmFnisXQ](https://www.youtube.com/embed/iZqHmFnisXQ)

因此，**我们可以使用以下命令创建开关包，其中包含运行我们的服务器应用程序**所需的依赖项:

另外，**我们可以通过键入:
从我们的 Vapor 应用程序 Swift 包中创建一个 Xcode 项目**

```
vapor xcode 
```

因此，我们可以打开新创建的 Xcode 项目。

[![Switching the target to Run](img/dd663284d3896b946be3ac0f16220d75.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vzCfgYoR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/learningswift.brightdigit.com/wp-content/uploads/sites/2/2019/03/macos_xcode_switch_target.jpg%3Fw%3D440%26ssl%3D1) 

<figure>

<figcaption>切换目标运行</figcaption>

</figure>

[![Switching the target to Run](img/a98b559d2e4ee896a7df1ec2ed27a387.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QeSj97Dl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/learningswift.brightdigit.com/wp-content/uploads/sites/2/2019/03/macos_xcode_run.jpg%3Fw%3D440%26ssl%3D1) 

<figure>

<figcaption>建立并运行运行目标</figcaption>

</figure>

[https://www.youtube.com/embed/kZKP2MiFaLQ](https://www.youtube.com/embed/kZKP2MiFaLQ)

之后，将左上角的目标切换到*运行*并按下*播放按钮*运行应用程序。因此，您应该在底部看到一条日志消息，说明 http 服务器在端口 8080 启动。

因此，转到终端并运行命令:

```
curl http://localhost:8080/hello 
```

因此，您应该会收到消息:

```
%Hello, world!% 
```

[https://www.youtube.com/embed/bRXTT8K-lP8](https://www.youtube.com/embed/bRXTT8K-lP8)

总之，我们已经成功地使用提供的带有 *new* 和 *xcode* 命令的 Vapor 模板来创建 Swift 包并在 xcode 中构建应用程序。然而，该应用程序只使用 SQLite 作为我们的数据库存储。相反，让我们转而使用一个更常见的数据库:PostgreSQL。

### 为 PostgreSQL 设置

PostgreSQL 是我最喜欢的基于关系 SQL 的数据库服务器。因此，我们将考虑将当前的 Vapor 项目转换为使用 PostgreSQL，而不是默认的 SQLite。因此，请确保您已经安装了 PostgreSQL。因此，如果你没有，你可以用下面的命令来安装 PostgreSQL:

```
brew install postgresql 
```

[https://www.youtube.com/embed/KeLHTQrZXNI](https://www.youtube.com/embed/KeLHTQrZXNI)

此外，从终端运行服务器:

```
pg_ctl -D /usr/local/var/postgres start 
```

同样，您可以从终端使用
来停止服务器

```
pg_ctl -D /usr/local/var/postgres stop 
```

另一方面，如果您希望使用 launchd 将 PostgreSQL 作为后台服务运行，您可以使用`brew services`命令:

```
brew services start postgresql 
```

最重要的是，一旦 PostgreSQL 启动，我们将需要添加数据库和用户供我们的应用程序使用。

### 设置数据库和用户

由于切换了 PostgreSQL，我们需要添加我们的 Vapor 应用程序将使用的用户和数据库。因此，让我们运行基于 PostgreSQL 终端的前端，方法是到终端键入:

```
psql -d postgres 
```

之后，在`psql`提示符下，用
创建数据库

```
create database app_collection; 
```

同样，我们需要创建一个用户。为了澄清，我们通过键入:
来创建一个没有
密码的用户

```
create user app_collection; 
```

随后使用
授予该用户所需的权限

```
grant all privileges on database app_collection to app_collection; 
```

[https://www.youtube.com/embed/gmKSy71PxWM](https://www.youtube.com/embed/gmKSy71PxWM)
因此，我们的数据库应该可以访问了。但是，我们需要更新代码，特别是 Swift 包，以使用 PostgreSQL。

### 更新 PostgreSQL 的代码

#### 用 PostgreSQL 更新 Package.swift

为了在我们的后端应用程序中使用 PostgreSQL，我们需要更新 Package.swift 文件以使用 PostgreSQL，而不是 SQLite。

因此，打开 Package.swift 并通过将行从
更改为

```
.package(url: "https://github.com/vapor/fluent-sqlite.git", from: "3.0.0") 
```

到线:

```
.package(url: "https://github.com/vapor/fluent-postgresql.git", from: "1.0.0") 
```

为了澄清，我们将使用 [Fluent](https://docs.vapor.codes/3.0/fluent/getting-started/) ，Vapor 的 ORM 框架，以及 PostgreSQL。换句话说，我们正在用 PostgreSQL 的驱动程序替换 SQLite 的 Fluent 驱动程序。同样，如果你能在 Fluent 的文档中读到更多关于不同驱动程序的细节以及它们的设置是如何工作的。

因此，我们还需要更新*应用目标*，以使用新的
*FluentPostgreSQL* 依赖关系。因此更改行:

```
.target(name: "App", dependencies: ["FluentSQLite", "Vapor"]), 
```

到线:

```
.target(name: "App", dependencies: ["FluentSQLite", "Vapor"]), 
```

[https://www.youtube.com/embed/j3Z1HxSMVWU](https://www.youtube.com/embed/j3Z1HxSMVWU)

除了更新 *Package.swift* 文件之外，我们需要实际更新我们的 Xcode 项目以使用新的依赖项。

#### 更新包

因此，为了用新的依赖项获取和更新包，并更新 Xcode 项目文件，我们可以使用
再次运行 *xcode* 命令

```
vapor xcode 
```

因此，vapor 获取了 Fluent PostgreSQL 驱动程序并更新了 Xcode 项目。因此，我们可以通过更新代码来前进。

#### 更新代码

因此，打开更新 Xcode 项目。由于我们已经删除了 Fluent SQLite 驱动程序，我们应该不再能够构建该项目。因此，我们需要更新我们的模型，以使用 PostgreSQL 以及配置中使用的提供者、数据库和迁移。所以，打开 *configure.swift* 。

首先，让我们从:
开始更新文件顶部的导入

```
import FluentSQLite 
```

致:

```
import FluentPostgreSQL 
```

其次，通过更新行来更新提供者:

```
try services.register(FluentSQLiteProvider()) 
```

至

```
try services.register(FluentPostgreSQLProvider()) 
```

此外，通过将行从
更改为

```
// Configure a SQLite database
let sqlite = try SQLiteDatabase(storage: .memory)

// Register the configured SQLite database to the database config.
var databases = DatabasesConfig()
databases.add(database: sqlite, as: .sqlite)
services.register(databases) 
```

致:

```
// Configure a PostgreSQL database
let postgreSQLConfig = PostgreSQLDatabaseConfig(hostname: "localhost", username: "app_collection")
let postgreSQL = PostgreSQLDatabase(config: postgreSQLConfig)

// Register the configured PostreSQL database to the database config.
var databases = DatabasesConfig()
databases.add(database: postgreSQL, as: .psql)
services.register(databases) 
```

换句话说，我们用配置了用户名 *app_collection* 的 PostgreSQL 本地数据库替换了基于内存的 SQLite 数据库。

为了澄清，`PostgreSQLDatabaseConfig` [初始化器](https://api.vapor.codes/postgresql/latest/PostgreSQL/Structs/PostgreSQLDatabaseConfig.html#/s:10PostgreSQL0A17SQLDatabaseConfigVACSS8hostname_Si4portSS8usernameSSSg8databaseAG8passwordAA0A13SQLConnectionC09TransportD0V9transporttcfc)可以有多个参数，在这种情况下我们使用默认端口号和与用户名匹配的默认数据库名，在这种情况下是 *app_collection* 。

最后，在 *configuration.swift* 中，更新`Todo`模型的迁移。澄清一下，[迁移](https://docs.vapor.codes/3.0/fluent/migrations/)用于创建和更新 Fluent 使用的表和关系。因此，在这种情况下，我们只需要更改这一行中的参数:

```
migrations.add(model: Todo.self, database: .sqlite) 
```

到这行:

```
migrations.add(model: Todo.self, database: .psql) 
```

同样，我们需要通过打开 *Todo.swift* 来更改模型使用的子类。之后，再次修改顶部的导入语句，然后从:
更新模型子类

```
final class Todo: SQLiteModel { 
```

至

```
final class Todo: PostgreSQLModel { 
```

[https://www.youtube.com/embed/EL98HVIzV9M](https://www.youtube.com/embed/EL98HVIzV9M)

因此，我们可以继续运行 Vapor 应用程序。

### 运行代码

总而言之，既然已经在我们的机器上设置了 PostgreSQL 数据库，更新了 Swift 包依赖项，并相应地更新了代码；我们可以构建并运行 Vapor 应用程序。因此，在 Xcode 中，再次运行 *Run* 目标。之后，我们再去终端 app 测试应用。例如我们可以再次运行:

```
curl http://localhost:8080/hello 
```

但是，我们也可以通过运行:
来添加一个`Todo`项

```
curl -X POST -H "Content-Type: application/json" -d '{"title": "example"}' http://localhost:8080/todos 
```

因此，我们可以通过调用:
来验证添加了`Todo`项

```
curl http://localhost:8080/todos 
```

结果，我们得到 JSON 响应:

```
[{"id":1,"title":"example"}] 
```

随后，让我们将它部署到云中。首先，我们会调查赫罗库。

### 向 Heroku 部署蒸汽

除了为 PostgreSQL 设置我们的应用程序，我们还将继续部署它 Heroku。最重要的是，Heroku 提供了最简单的启动和运行方式。此外，它的免费层提供了足够的插件和服务，我们可以简单地开始。因此，让我们从为 Heroku 设置项目开始。

### 为 Heroku 准备蒸汽包

重要的是，确保你在 Heroku 有一个帐户设置。之后，再次进入终端，在项目目录下，我们可以简单运行命令:

```
vapor heroku init 
```

作为运行该命令的结果，我们创建了一个 Heroku 应用程序并添加了:

*   用于 Heroku 部署的 Vapor 构建包
*   Heroku 的 git 遥控器
*   heroku 运行应用程序所需的配置文件

您在[Procfile 文档](https://devcenter.heroku.com/articles/procfile)中读到的 proc file 包含下面一行:

```
web: Run serve --env production --port $PORT --hostname 0.0.0.0 
```

[https://www.youtube.com/embed/8piDsDi6PNo](https://www.youtube.com/embed/8piDsDi6PNo)

此外，当您运行该命令时，您不需要提供任何定制。然而，我们需要将 PostgreSQL 添加到 Heroku 应用程序中。

### 将 PostgreSQL 添加到 Heroku 实例

类似地，由于我们已经通过终端在 Heroku 中设置了 Vapor 应用程序，我们需要将 PostgreSQL 添加到我们的应用程序中。因此，我们可以通过运行:
来添加 Heroku repo 并使用 Homebrew 安装 *heroku* 命令

```
brew tap heroku/brew && brew install heroku 
```

因此，我们可以通过运行命令:
来添加 PostgreSQL

```
heroku addons:create heroku-postgresql:hobby-dev 
```

运行该命令的结果是，我们在应用程序中添加了一个 PostgreSQL 的[免费爱好级实例。](https://www.heroku.com/postgres#heroku-postgres-hobby)

[https://www.youtube.com/embed/rprznYi-kKQ](https://www.youtube.com/embed/rprznYi-kKQ)

因此，Heroku 将为数据库添加一个名为`DATABASE_URL`的环境变量。因此，我们需要更新代码来使用它。

### 为 Heroku PostgreSQL 更新代码

因为我们已经将 PostgreSQL 添加到 Heroku 实例中，所以我们需要更新我们的代码来使用新数据库 url 的环境变量，
`DATABASE_URL`。因此，打开 *configure.swift* 。之后，更改我们设置数据库的代码行:

```
let postgreSQLConfig = PostgreSQLDatabaseConfig(hostname: "localhost", username: "app_collection") 
```

致:

```
let postgreSQLConfig : PostgreSQLDatabaseConfig

if let url = Environment.get("DATABASE_URL") {
  postgreSQLConfig = PostgreSQLDatabaseConfig(url: url)!
} else {
  postgreSQLConfig = PostgreSQLDatabaseConfig(hostname: "localhost", username: "app_collection")
} 
```

[https://www.youtube.com/embed/J7VILggBt0Q](https://www.youtube.com/embed/J7VILggBt0Q)

由于更改了代码，我们将检查是否有一个名为`DATABASE_URL`的环境变量。换句话说，我们使用静态方法`Environment.get(_:)`来获取字符串值，如果它存在的话。其次，如果变量不存在，我们假设它在本地运行，并且使用默认设置。为了澄清，我们使用无条件展开，因为我们宁愿它触发一个运行时错误，而不是试图使用默认设置。最后，让我们继续部署我们的应用程序。

### 将蒸汽包部署到 Heroku

类似地，我们可以在终端中用两个命令之一将应用程序部署到 Heroku。首先，您可以使用命令:
用 git 直接推送

```
git push heroku master 
```

其次，可以用 Vapor CLI 命令:
推送

```
vapor heroku push 
```

简而言之，这两个命令都会:

*   首先，把你的代码推给 Heroku
*   其次，构建应用程序
*   最后，运行应用程序(基于 Procfile 中的命令)

因此，如果我们的应用程序实例名为*damp-spire-56788 *，我们应该用`curl` :
调用我们的应用程序

```
> curl http://damp-spire-56788.herokuapp.com/hello
Hello, world!%
> curl -X POST -H "Content-Type: application/json" -d '{"title": "example"}' http://damp-spire-56788.herokuapp.com/todos
{"id":1,"title":"example"}%
> curl http://damp-spire-56788.herokuapp.com/todos 
[{"id":1,"title":"example"}]% 
```

[https://www.youtube.com/embed/a0IJUTOJafo](https://www.youtube.com/embed/a0IJUTOJafo)

简而言之，我们可以通过以下方式为 Heroku 设置应用程序

*   首先，使用 Vapor CLI 设置实例、构建包、Procfile 和 git remote
*   其次，使用在 Heroku 中设置 PostgreSQL 实例
*   之后，更新代码以使用数据库 url 的环境变量来访问 Heroku PostgreSQL 实例
*   最后，将代码推送到 Heroku 并测试应用程序

类似地，现在我们知道了如何部署到 Heroku，让我们看看需要做些什么来设置我们自己的 Ubuntu 服务器来托管我们的 Vapor 应用程序。

### 为 Ubuntu 设置 Vapor

同样，我展示了如何为 Heroku 设置我们的 Vapor 应用程序；我们将看看设置一个 Ubuntu 服务器需要什么。在这种情况下，我们使用的是最新的 LTS 版 Ubuntu，也就是 Bionic Beaver 18.04。然而，根据[文档](https://docs.vapor.codes/3.0/install/ubuntu/)，Vapor 确实支持 14.04 版本(可信的 Tahr)以及更高版本，包括 18.10 版本(宇宙
乌贼)。

### 安装先决条件

对于新的服务器设置，我们需要安装几个软件包。然而，首先我们需要添加 Vapor 的 APT repo。因此，在服务器上(以 root 用户身份)，运行以下命令:

```
eval "$(curl -sL https://apt.vapor.sh)" 
```

之后，我们可以继续安装将用于设置服务器的软件:

*   **git** —用于获取我们的应用程序的代码
*   **nginx** —我们的 http 服务器
*   **postgresql** —我们的数据库服务器
*   **监控器**——用于监控和执行应用程序
*   **swift** 和**vapor**——用于构建和运行应用程序

因此，我们可以用:
安装应用程序

```
apt install git supervisor postgresql swift vapor nginx 
```

[https://www.youtube.com/embed/Ul4hL9vP06g](https://www.youtube.com/embed/Ul4hL9vP06g)

### 设置 PostgreSQL 数据库

与我们如何在 Heroku 上使用`DATABASE_URL`类似，我们将在 Ubuntu 中设置环境变量。

#### 更新代码

因此，打开 Xcode，在我们的项目中返回到`configure.swift`。之后，在`import`语句之间，但在`configure`函数之前，添加一个结构来存储 PostgreSQL 的默认值:

```
public struct PostgresDefaults {
  public static let hostname = "localhost"
  public static let username = "app_collection"
  public static let port = 5432
} 
```

为了清楚起见，这个结构存储了我们用于 PostgreSQL 配置的默认主机名、用户名和端口

此外，将 else 语句中的行从:

```
postgreSQLConfig = PostgreSQLDatabaseConfig(hostname: "localhost", username: "app_collection") 
```

致:

```
let hostname = Environment.get("DATABASE_HOSTNAME") ?? PostgresDefaults.hostname
let username = Environment.get("DATABASE_USERNAME") ?? PostgresDefaults.username
let database = Environment.get("DATABASE_DATABASE")
let password = Environment.get("DATABASE_PASSWORD")

let port : Int

if let portString = Environment.get("DATABASE_PORT") {
  port = Int(portString) ?? PostgresDefaults.port
} else {
  port = PostgresDefaults.port
}

postgreSQLConfig = PostgreSQLDatabaseConfig(hostname: hostname, port: port, username: username, database: database, password: password, transport: .cleartext) 
```

总而言之，这段代码从环境变量中获取主机名、用户名、数据库、密码和端口。因此，如果有任何值无效或不存在，它会返回到`PostgresDefaults`结构中的默认设置或 null，让`PostgreSQLDatabaseConfig`使用它的默认值。同样，这段代码仍然可以在 Heroku(使用`DATABASE_URL`)和 localhost(使用默认设置)上运行。

[https://www.youtube.com/embed/xb3LNa_eADo](https://www.youtube.com/embed/xb3LNa_eADo)

然而，在为应用程序设置环境变量之前，我们需要设置数据库。

#### 添加数据库和用户

因此，作为 root 用户，sudo 以`postgres`用户的身份运行`psql` :

```
sudo -u postgres psql 
```

之后，运行以下 sql 语句:

```
create database app_collection;
create user app_collection with encrypted password 'app_collection_pw';
grant all privileges on database app_collection to app_collection; 
```

澄清一下，我们正在创建:

*   名为 *app_collection* 的数据库
*   用户名为 *app_collection* ，密码为 *app_collection_pw*
*   授予用户对我们创建的数据库的所有权限

[https://www.youtube.com/embed/hY1gOcB4XbE](https://www.youtube.com/embed/hY1gOcB4XbE)

由于设置了数据库，我们可以开始设置*主管*。

### 设置主管

因为我们使用 supervisor，所以我们将设置一个 Linux 用户来运行实际的应用程序。

#### 添加一个 Linux 用户

再次以 root 用户身份运行以下命令:

```
adduser app_collection 
```

为了澄清，我们正在设置名为`app_collection`的用户，它不需要
任何其他元数据(全名、房间号等...).因此，已经设置了
用户，让我们拉并构建代码。

#### 拉式回购

在 Linux 中设置好用户后，继续以超级用户的身份作为新用户:

```
su -l app_collection 
```

之后，作为新用户，克隆回购:

```
git clone https://github.com/brightdigit/swift-vapor-app-collection-sample.git app 
```

随后，转到目录并构建应用程序:

```
cd app
vapor build 
```

[https://www.youtube.com/embed/RU3jJUe66uE](https://www.youtube.com/embed/RU3jJUe66uE)

简而言之，我们已经从 repo 中克隆了代码并构建了应用程序。因此，我们可以继续配置 supervisor。

#### 配置主管

虽然我们的应用程序将服务于我们的 Rest API， *nginx* 将代理那个
连接，*主管*将监视和控制实际的 Vapor 应用程序过程。因此，让我们为 Vapor 应用程序创建一个配置。换句话说，作为 root，在这里创建一个文件:

```
/etc/supervisor/conf.d/app_collection.conf 
```

之后，将此文本粘贴到配置文件:

```
[program:app_collection]
environment =
        DATABASE_PASSWORD="app_collection_pw",
command=vapor run --port=3000 --env=production
directory=/home/app_collection/app/ 
autorestart=true
user=app_collection             
stdout_logfile=/var/log/supervisor/%(program_name)-stdout.log
stderr_logfile=/var/log/supervisor/%(program_name)-stderr.log 
```

澄清:

*   该过程的名称将为*程序:应用程序集合*
*   我们为数据库密码设置了一个环境变量
*   命令是从我们的代码目录运行 Vapor
*   应用程序失败时自动重启
*   作为我们的新用户运行
*   最后，我们为应用程序输出和错误设置了一个路径

随后，我们可以继续使用下面的
命令更新 supervisor by:

```
supervisorctl reread
supervisorctl update 
```

[https://www.youtube.com/embed/-dCqEE3L0nk](https://www.youtube.com/embed/-dCqEE3L0nk)

之后，您可以通过从端口 3000:
调用应用程序来测试 supervisor 是否成功

```
curl http://localhost:3000/hello 
```

简而言之，我们已经将 supervisor 中的 Vapor 应用程序设置为在端口 3000 上运行，并将数据库密码设置为环境变量。因此，让我们设置 *nginx* 来代理我们的应用程序。

### 设置 Nginx

对于我们的 http 服务器，我们将使用 *nginx* 。最重要的是，我们将为我们的应用程序设置一个主机名。因此，我们使用 nginxconfig.io 来设置配置文件。因此，你可以从[这里](https://nginxconfig.io/?0.domain=app_collection.local&0.redirect=false&0.https=false&0.php=false&0.proxy&0.proxy_pass=http://localhost:3000&0.root=false)下载[我们正在使用的特定 nginx 配置](https://nginxconfig.io/?0.domain=app_collection.local&0.redirect=false&0.https=false&0.php=false&0.proxy&0.proxy_pass=http://localhost:3000&0.root=false)
。此外，在下载之前，请随意更改主机名。

之后，解压文件:

```
unzip nginxconfig.io-app_collection.local.zip 
```

首先，我们可以以 root 用户的身份将文件移动到合适的位置:

```
mv nginx.conf /etc/nginx/nginx.conf
mv sites-available/app_collection.local.conf /etc/nginx/sites-available
ln -s /etc/nginx/sites-available/app_collection.local.conf /etc/nginx/sites-enabled
mv nginxconfig.io /etc/nginx/ 
```

其次，现在我们已经转移了配置文件，重启 nginx:

```
service nginx restart 
```

[https://www.youtube.com/embed/qUrDtoHI-zw](https://www.youtube.com/embed/qUrDtoHI-zw)

最重要的是，如果您使用的是`.local`主机名，请确保在您的网络上设置主机名。也就是说，确保可以从本地开发计算机访问主机名。例如，在 macOS 或 Linux 上，你需要编辑[文件`/etc/hosts`和](https://en.wikipedia.org/wiki/Hosts_(file))。另一方面，如果这是一个公共主机名，请确认您已经相应地更新了 dns。

在您验证 dns 或 hosts 文件已更新后，我们可以继续测试应用程序:

```
> curl http://app_collection.local/hello
Hello, world!%
> curl -X POST -H "Content-Type: application/json" -d '{"title": "example"}' http://app_collection.local/todos
{"id":1,"title":"example"}%
> curl http://app_collection.local/todos 
[{"id":1,"title":"example"}]% 
```

简而言之，我们已经成功地在 Ubuntu 服务器上安装了应用程序。

### 还有更多…

总之，我们已经成功地:

*   设置我们的 **Mac，以便使用 Vapor 开发**服务器端 Swift 应用程序
*   将初学者模板从 SQLite 转换为**使用 PostgreSQL**
*   将代码更新为**针对不同的服务器环境使用环境变量**
*   将应用程序安装并部署到 **Heroku**
*   设置并部署到一个 **Ubuntu** 服务器

Vapor 将减少您快速发展中的摩擦，并拥有有效的社区支持。要获得完整的指南和视频迷你课程，请填写[此表格](http://eepurl.com/gku85T)和**让我知道您有兴趣了解的其他 Vapor 主题**，例如:

*   建立**复杂的数据库模型**和关系
*   如何配置**高级 Rest API 路由**
*   使用 Fluent ORM 和 Swift Futures 编写**复杂查询**
*   设置**认证**

我期待着听到你的反馈，我会让你随时更新。**
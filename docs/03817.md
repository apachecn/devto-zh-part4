# 剩下的去吧

> 原文：<https://dev.to/remast/go-for-the-rest-1jp9>

Go 编程语言非常适合 RESTful web 应用程序。选择正确的 Go web 框架并不是一件容易的事情。幸运的是，这个任务已经解决了。选择 Go web 框架 [buffalo](https://gobuffalo.io) 就可以开始了。还不信服？学习如何用 [buffalo](https://gobuffalo.io) 构建一个 RESTful web 应用程序。

[Buffalo](https://gobuffalo.io) 是在 Go 中开发 web 应用的一个完整的生态系统。Buffalo 结合了路由、模板和测试，基于著名的[模型-视图-控制器模式](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)构建 web 应用程序。foundation 是一个具有热重载功能的强大工具，一个视图和控制器的生成器，以及用于压缩 JavaScript 或数据库迁移等常见任务的工具。Buffalo 将现有的库(如 Gorilla web toolkit)与 Go web 开发的最佳实践相结合。buffalo 提供了一个统一且一致的项目结构，包括包和目录，直到处理数据库会话。所以让我们开始吧。

# 安装水牛

我们将使用新的 [Go 模块](https://blog.golang.org/using-go-modules)进行依赖管理。所以让我们使用环境变量`GO111MODULE=on`来激活它。现在我们可以用`go get -u -v github.com/gobuffalo/buffalo/buffalo`装水牛了。在您的 shell 中执行命令`buffalo`应该会显示帮助。

# 创建并运行 RESTful web 应用程序

现在我们创建第一个名为`rowdy`的 buffalo RESTful web 应用程序。命令`buffalo new rowdy --api`在目录`rowdy`中创建新的 RESTful buffalo web 应用程序(没有前端)。我们可以用命令`buffalo dev`(来自目录`rowdy`)运行应用程序。该应用程序可从 http://localhost:3000 获得。但是等等，那没用！生成的应用程序需要一个正在运行的数据库。所以我们用`docker run --name rowdy_db -e POSTGRES_PASSWORD=postgres -d postgres`开始一个 PostgreSQL 数据库。现在，您应该能够启动应用程序并访问 web 根目录下的 REST API，比如说`"Welcome to Buffalo"`。

您可以在开发过程中保持应用程序运行。Buffalo 将自动获取源代码中的更改，并根据需要重新构建和重启应用程序。

# 通过 App 引导

buffalo 应用程序的入口点是应用程序根目录中的`main.go`。main.go 是应用程序的主包，通过执行 buffalo struct [`buffalo.App`](https://godoc.org/github.com/gobuffalo/buffalo#App) 的方法`Serve`来启动应用程序。buffalo 应用程序本身是在包 actions 中的`app.go`中创建的。

app.go 是音乐播放的地方。在`app.go`中，所有的路线和中间件都被注册。对于每条路线，都会记录一个操作。动作是 MVC 模式的控制器层。目前我们只有一个动作，那就是`HomeHandler`。通过`app.GET("/", HomeHandler)`动作 HomeHandler 被注册在 app.go 路径`/`。`HomeHandler`本身在 home.go 的包动作中声明，相关的测试在`home_test.go`中。

包模型是针对 MVC 的模型层的。Buffalo 假设模型层是应用程序的持久层，它运行在 postgres 这样的关系数据库上。该模型层基于 buffalo pop，这是一个受活动记录启发的 Go 持久层。

目录迁移完全是为了维护关系数据库的模式。这里存储了数据库迁移框架 [fizz](https://gobuffalo.io/en/docs/db/fizz) 的脚本。但我们稍后会谈到这一点。

然后是目录装置，配置和诈骗。Fixtures 是为测试创建数据库内容的脚本。Config 用于 buffalo 代码生成器的配置。Grifts 是一些小脚本，用于自动执行常见任务，如列出所有路线。

为了完成我们的指南，我们来看看生成的配置文件。我们的应用程序中最重要的配置文件是 database.yml，其中包含几种环境的数据库凭证。然后是 Dockerfile 来创建我们的应用程序的 docker 容器。

# 创建健康检查动作

现在让我们创建第一个动作，在 route `/health/check`进行健康检查。我们通过运行`buffalo generate action health check --skip-template`使用 buffalo 代码生成器生成健康检查操作的框架。这生成了一个名为`HealthCheck`的动作。生成器命令的第一个参数是动作的名称，第二个参数 check 是处理程序的名称。因为我们构建了一个 RESTful API，所以我们的动作不需要 HTML 模板，并且不生成带有参数`--skip-template`的模板。Buffalo 在目录操作中创建了文件`health.go`和`health_test.go`。生成器还在`app.go`中注册了路径`/health/check`的新动作。

一个水牛动作需要实现 [`buffalo.Handler`](https://godoc.org/github.com/gobuffalo/buffalo#Handler) 接口。术语 action 和 handler 是 MVC 模式的控制器层的同义词。一看`home.go`显示了生成的代码:

```
func HealthCheck(c buffalo.Context) error {
    return c.Render(200, r.HTML("health/check.html"))
} 
```

Enter fullscreen mode Exit fullscreen mode

生成的处理程序试图将模板`health/check.html`呈现为 HTML。因为我们构建了一个 RESTful API，我们没有模板，所以这不能工作。我们将实现更改为`c.Render(200, r.String("Up and running!"))`,以便它返回一个纯文本字符串。

```
func HealthCheck(c buffalo.Context) error {
    return c.Render(200, r.String("Up and running!"))
} 
```

Enter fullscreen mode Exit fullscreen mode

# 测试健康检查

现在我们需要为我们的健康检查修复测试。整个 buffalo 应用程序在测试中是可用的。在测试中，您可以针对应用程序运行请求，甚至可以通过 HTTP 会话访问它所运行的数据库。

为了测试健康检查，我们运行请求路径`/health/check`并验证 HTTP 状态代码。这样做的代码是:

```
package actions

func (as *ActionSuite) Test_Health_Check() {
    result := as.HTML("/health").Get()

    as.Equal(200, result.Code)
    as.Contains(result.Body.String(), "Up and running")
} 
```

Enter fullscreen mode Exit fullscreen mode

# 更多关于行动和路线

配置路径时，您可以在路径中使用占位符和正则表达式，如`app.GET("/account/{category}/{id:[0-9]+}", AccountHandler)`。因为它是基于 Gorilla web toolkit 的，所以你可以充分利用 Gorilla 多路复用器。

在处理程序本身中，您可以完全访问 [`buffalo.Context`](https://godoc.org/github.com/gobuffalo/buffalo#Context) 。使用 buffalo 上下文，您可以呈现 JSON 或 HTML，并使用方法`c.Params()`和`c.Param(string)`访问路径和查询参数。您也可以使用`c.Logger().Info("Health checked by: %v", c.Request().Host)`访问记录器并记录健康检查请求。

使用命令`buffalo routes`查看所有已注册的路线，该命令显示:

```
METHOD | PATH           | ALIASES | NAME            | HANDLER
------ | ----           | ------- | ----            | -------
GET    | /              |         | rootPath        | rowdy/actions.HomeHandler
GET    | /health/check/ |         | healthCheckPath | rowdy/actions.HealthCheck 
```

Enter fullscreen mode Exit fullscreen mode

## CRUD 资源

为了便于开发 CRUD 功能，buffalo 提供了资源。buffalo 资源总是包含创建、显示、更新、销毁和在路由器中列出标准化路径的操作。资源总是 RESTful 的，从不使用模板。

现在我们创建一个资源来管理与`buffalo generate resource concert`的音乐会。布法罗为我们创建了几个文件。在包动作的文件`concert.go`中实现 concert 资源，以及`concert_test.go`中的测试。包含测试的包模型中`concert.go`pop 的数据库映射。最后但同样重要的是，在目录迁移中，两个 fizz 脚本在我们的数据库模式中为 concerts 创建了一个表。使用`app.Resource("/concerts", ConcertsResource{})`将音乐会资源注册在`app.go`中。

查看 buffalo routes 的输出，我们可以看到新音乐会资源的所有路线。

```
METHOD | PATH                    | ALIASES | NAME            | HANDLER
------ | ----                    | ------- | ----            | -------
GET    | /                       |         | rootPath        | rowdy/actions.HomeHandler
GET    | /concerts/              |         | concertsPath    | rowdy/actions.ConcertsResource.List
POST   | /concerts/              |         | concertsPath    | rowdy/actions.ConcertsResource.Create
GET    | /concerts/{concert_id}/ |         | concertPath     | rowdy/actions.ConcertsResource.Show
PUT    | /concerts/{concert_id}/ |         | concertPath     | rowdy/actions.ConcertsResource.Update
DELETE | /concerts/{concert_id}/ |         | concertPath     | rowdy/actions.ConcertsResource.Destroy
GET    | /health/check/          |         | healthCheckPath | rowdy/actions.HealthCheck 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看看生成的处理程序方法`Show`。我们看到 buffalo 通过`c.Value("tx").(*pop.Connection)`从 buffalo 上下文中获得数据库连接。然后，buffalo 使用 pop 从数据库中加载 concert 模型。concert 模型的 id 作为路径参数提供。最后用`c.Render(200, r.Auto(c, concert))`返回加载的音乐会模型。根据协商的内容类型，这要么是 JSON，要么是 XML。

```
// GET /concerts/{concert_id}
func (v ConcertsResource) Show(c buffalo.Context) error {
    // Get the DB connection from the context
    tx, ok := c.Value("tx").(*pop.Connection)
    if !ok {
        return errors.WithStack(errors.New("no transaction found"))
    }

    // Allocate an empty Concert
    concert := &models.Concert{}

    // To find the Concert the parameter concert_id is used.
    if err := tx.Find(concert, c.Param("concert_id")); err != nil {
        return c.Error(404, err)
    }

    return c.Render(200, r.Auto(c, concert))
} 
```

Enter fullscreen mode Exit fullscreen mode

## 错误处理

正确处理错误可能很棘手。但是水牛城会掩护你。错误通常发生在处理程序中。处理程序有两种处理它们的选择:

1.  从类似`errors.New("Boom!")`的处理程序返回一个正常的 Go 错误。然后，应用程序返回内部服务器错误的 HTTP 状态代码 500。

2.  使用 bufallo context 方法处理类似`return context.Error(501, errors.New("That blew up!"))`的错误。这样，应用程序返回自定义状态代码 501。

无论哪种方式，应用程序都会继续运行，错误会被记录下来并以 JSON 或 XML 的形式返回。如果 buffalo 在开发模式下运行(环境`variable GO_ENV="development"`或从`buffalo dev`开始)，这将包含一个错误堆栈跟踪。

## 中间件

Buffalo 应用程序拥有许多 Go web 开发的最佳实践。这主要是各种中间件的特点。中间件是在 HTTP 处理程序(在 buffalo 中称为 action)之前或之后运行的代码。中间件是 Go web 开发的一个通用概念。关于中间件概念的一个很好的读物是亚历克斯·爱德华兹的《制作和使用 HTTP 中间件》。

Buffalo 从一开始就集成了许多有用的中间件到 [`buffalo.App`](https://godoc.org/github.com/gobuffalo/buffalo#App) 中。例如，防止您的应用程序在死机或出错后崩溃的中间件。在我们生成的 app 中，中间件注册在`actions/app.go`。一个例子是`app.Use(forceSSL)`，它注册了一个中间件，将 HTTP 请求重定向到 HTTPS。

对于特定的路线，也可以跳过中间件。我们可以用它来跳过健康检查路径的认证中间件。命令`buffalo task middleware`显示每条路由注册的中间件。

## 配置

buffalo 应用程序使用环境变量进行配置，如[十二因素应用程序](https://12factor.net/config)中所建议的。该配置由 buffalop 包 [envy](https://github.com/gobuffalo/envy) 提供动力。

有预定义的环境变量，如当前环境的`GO_ENV`(如开发、测试、生产)或`HOST` ( `localhost`)和`PORT` (3000)。您也可以使用自定义变量。您可以在`envy.Get("CUSTOM_SETTING", "default-value")`中使用可选的环境变量。或带有`envy.MustGet("MY_REQUIRED_OPTION")`的强制变量，如果变量未设置，将导致错误。

您也可以从文件`.env`中读取环境变量。Buffalo 在当前目录中查找名为`.env`的文件，并对其进行评估。如果你创建一个只有行`dotenv`的`.envrc`(见 [`.env`文件](https://github.com/direnv/direnv/issues/284))，这就很好地与 [direnv](https://direnv.net/) 集成了。你可以用这个让 buffalo 读取你的本地开发设置，当然你不应该签入。

## 插件

Buffalo 可以使用插件进行扩展。数据库框架 pop 是作为 buffalo 插件实现的。还有针对 [kubernetes](https://toolkit.gobuffalo.io/tools/69219770-87c4-4e53-9b6e-92f81ab819e4/) 或者 [Azure](https://toolkit.gobuffalo.io/tools/d299aa2d-03be-4df6-bd05-9b19d3f19ae6/) 的插件。buffalo 网站包含一个可用插件的[列表](https://toolkit.gobuffalo.io/tools?topic=plugin)。

## 打造 App

要构建应用程序，请运行`buffalo build`。Buffalo 编译这个应用程序，并在目录`build`中创建一个独立的二进制文件`rowdy`。

## 把剩下的包起来

我们学习了如何使用遵循 MVC 模式的 buffalo 框架构建 RESTful 应用程序。模型层由使用 pop 映射到 PostgreSQL 数据库的结构组成。动作和资源构成了控制器层。我们为 concerts 创建了一个简单的健康检查动作和一个 CRUD 资源。我们还没有使用视图层，因为我们需要的只是将结构序列化为 XML 或 JSON。我们学习了如何处理错误、如何处理配置以及如何在 CI 环境中运行生产版本。

Buffalo 为 go 中的 web 应用程序提供了统一一致的结构，遵循 Go web 开发的最佳实践，涵盖了从开发到生产的整个生命周期。

然而，仍然有太多我们无法涵盖的内容。Buffalo 在构建前端方面有很多优势。或者可以使用水牛任务或者后台工作者。所有这些都被伟大的[水牛文献](https://gobuffalo.io/en/docs/index/)所涵盖。

当然，请继续关注，因为我很快会在这里传播更多的野牛之爱！
# 用 Go - Part 1 创建自以为是的 GraphQL 服务器

> 原文：<https://dev.to/cmelgarejo/creating-an-opinionated-graphql-server-with-go-part-1-3g3l>

让我们使用以下内容来制作一个自以为是的 GraphQL 服务器:

*   [Gin-gonic](https://gin-gonic.com) web 框架
*   用于 OAuth2 连接的 [Goth](https://github.com/markbates/goth)
*   [GORM](http://gorm.io) 为数据库形式
    *   [Gomigrate](https://gopkg.in/gormigrate.v1)
*   [GQLGen](https://gqlgen.com/) 轻松构建 GraphQL 服务器

这里假设您有:

*   至少，基础围棋知识，并已安装 go 1.12+。
*   VSCode(首选)或类似的 IDE

## 项目设置

我们将遵循 Go 标准项目布局，对于这项服务，看一看
规范，它是自以为是的，但作为一个很好的基础，我们可能
从它的指导方针中脱离一点。

首先在我们想要的任何地方创建一个目录:

```
$ mkdir go-gql-server
$ cd go-gql-server
/go-gql-server $ 
```

Enter fullscreen mode Exit fullscreen mode

让我们用
创建整个项目布局

```
$ mkdir -p {build,cmd/gql-server,internal/gql-server,pkg,scripts}
# directories are created 
```

Enter fullscreen mode Exit fullscreen mode

*   `internal/gql-server`将保存 gql 服务器的所有相关文件
*   `cmd/gql-server`将为服务器存放`main.go`文件，入口点将所有文件粘合在一起。

因为我们正在使用 go 1.12+，它将允许使用任何你想在
路径之外的目录，我们想使用`go modules`来初始化我们的项目
，用它我们必须运行:

```
$ go mod init github.com/cmelgarejo/go-gql-server # Replace w/your user handle
go: creating new go.mod: module github.com/cmelgarejo/go-gql-server 
```

Enter fullscreen mode Exit fullscreen mode

## 编码我们的 web 服务器

### 1。Web 框架:Gin

所以，现在我们可以开始向我们的项目添加包了！让我们从获取我们的
web 框架开始:`gin-gonic`

来自 gin-gonic.com:

> 什么是杜松子酒？ Gin 是一个用 Golang 编写的 web 框架。它有一个类似 T2 马提尼的 API，性能更好，速度快了 40 倍。如果你需要性能和良好的生产力，你会喜欢杜松子酒。

```
$ go get -u github.com/gin-gonic/gin
go: creating new go.mod: module cmd/gql-server/main.go 
```

Enter fullscreen mode Exit fullscreen mode

### 2。编写 web 服务器代码

所以让我们开始创建 web 服务器，继续，在`cmd/gql-server`
中创建一个`main.go`文件

```
$ vi cmd/gql-server/main.go
# vi ensues, I hope you know how to exit 
```

Enter fullscreen mode Exit fullscreen mode

并粘贴这个占位符代码:

```
package main

import (
    "log"
    "net/http"

    "github.com/gin-gonic/gin"
)

func main() {
    host := "localhost"
    port := "7777"
    pathGQL := "/graphql"
    r := gin.Default()
    // Setup a route
    r.GET("/ping", func(c *gin.Context) {
        c.JSON(http.StatusOK, "OK")
    })
    // Inform the user where the server is listening
    log.Println("Running @ http://" + host + ":" + port + pathGQL)
    // Print out and exit(1) to the OS if the server cannot run
    log.Fatalln(r.Run(host + ":" + port))
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你`go run cmd/gql-server/main.go`这个代码，它将调出一个 GIN 服务器
监听 [locahost:7777](http://localhost:7777/ping) 并在浏览器中得到一个 *OK* 输出
。有用！现在，让我们使用已经存在的
目录结构、`script`、`internal`和`pkg`文件夹来重构这段代码

*   `internal/handlers/ping.go`

```
package handlers

import (
    "net/http"

    "github.com/gin-gonic/gin"
)

// Ping is simple keep-alive/ping handler
func Ping() gin.HandlerFunc {
    return func(c *gin.Context) {
        c.String(http.StatusOK, "OK")
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   `pkg/server/main.go`

```
package server

import (
    "log"

    "github.com/gin-gonic/gin"
    "github.com/cmelgarejo/go-gql-server/internal/handlers"
)

var HOST, PORT string

func init() {
    HOST = "localhost"
    PORT = "7777"
}

// Run web server
func Run() {
    r := gin.Default()
    // Setup routes
    r.GET("/ping", handlers.Ping())
    log.Println("Running @ http://" + HOST + ":" + PORT )
    log.Fatalln(r.Run(HOST + ":" + PORT))
} 
```

Enter fullscreen mode Exit fullscreen mode

*   `cmd/gql-server/main.go`文件可以改成这样:

```
package main

import (
    "github.com/cmelgarejo/go-gql-server/pkg/server"
)

func main() {
    server.Run()
} 
```

Enter fullscreen mode Exit fullscreen mode

怎么样？一行代码就让我们的服务器用完了`pkg`文件夹

现在我们可以用一个脚本来构建服务器:

*   `scripts/build.sh` ( `/bin/sh`因为比较 obiquitus)

```
#!/bin/sh
srcPath="cmd"
pkgFile="main.go"
outputPath="build"
app="gql-server"
output="$outputPath/$app"
src="$srcPath/$app/$pkgFile"

printf "\nBuilding: $app\n"
time go build -o $output $src
printf "\nBuilt: $app size:"
ls -lah $output | awk '{print $5}'
printf "\nDone building: $app\n\n" 
```

Enter fullscreen mode Exit fullscreen mode

在跑步之前，确保你`chmod +x`它

```
$ chmod +x scripts/build.sh
# sets execution permission on file 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以开始构建我们的服务器了，就像这样:

```
$ .scripts/build.sh

Building: gql-server

real    0m0.317s
user    0m0.531s
sys     0m0.529s

Built: gql-server size:16M

Done building: gql-server 
```

Enter fullscreen mode Exit fullscreen mode

16M 的独立服务器，我认为还不错，这可能是它的
docker 图像的大小！好了，现在开始试验已经建成的东西:

```
$ ./build/gql-server
[GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.

[GIN-debug] [WARNING] Running in "debug" mode. Switch to "release" mode in production.
 - using env:   export GIN_MODE=release
 - using code:  gin.SetMode(gin.ReleaseMode)

[GIN-debug] GET    /ping                     --> github.com/cmelgarejo/go-gql-server/internal/handlers.Ping.func1 (3 handlers)
2019/07/13 00:00:00 Running @ http://localhost:7777/gql
[GIN-debug] Listening and serving HTTP on :7777
[GIN] 2019/07/13 - 00:00:00 | 200 |      38.936µs |             ::1 | GET      /ping 
```

Enter fullscreen mode Exit fullscreen mode

> 速度真快，39 ( *微*)秒。

我们可以进一步改进我们的服务器代码，让配置从一个
`.env`文件加载，让我们为我们的服务器创建一些`utils`:

*   `pkg/utils/env.go`

```
package utils

import (
    "log"
    "os"
    "strconv"
)

// MustGet will return the env or panic if it is not present
func MustGet(k string) string {
    v := os.Getenv(k)
    if v == "" {
        log.Panicln("ENV missing, key: " + k)
    }
    return v
}

// MustGetBool will return the env as boolean or panic if it is not present
func MustGetBool(k string) bool {
    v := os.Getenv(k)
    if v == "" {
        log.Panicln("ENV missing, key: " + k)
    }
    b, err := strconv.ParseBool(v)
    if err != nil {
        log.Panicln("ENV err: [" + k + "]\n" + err.Error())
    }
    return b
} 
```

Enter fullscreen mode Exit fullscreen mode

代码是不言自明的，如果一个 ENV 变量不存在，
程序将会崩溃，我们需要它们来运行。现在，改变:

*   `pkg/server/main.go`对此:

```
package server

import (
    "log"

    "github.com/gin-gonic/gin"
    "github.com/cmelgarejo/go-gql-server/internal/handlers"
    "github.com/cmelgarejo/go-gql-server/pkg/utils"
)

var host, port string

func init() {
    host = utils.MustGet("GQL_SERVER_HOST")
    port = utils.MustGet("GQL_SERVER_PORT")
}

// Run spins up the server
func Run() {
    r := gin.Default()
    // Simple keep-alive/ping handler
    r.GET("/ping", handlers.Ping())
    // Inform the user where the server is listening
    log.Println("Running @ http://" + host + ":" + port)
    // Print out and exit(1) to the OS if the server cannot run
    log.Fatalln(r.Run(host + ":" + port))

} 
```

Enter fullscreen mode Exit fullscreen mode

我们看到它是如何作为一个精心设计的项目开始成形的！

我们仍然可以做一些事情，比如使用这个
脚本在本地运行服务器:

*   `scripts/run.sh`(别忘了`chmod +x`这个也是)

```
#!/bin/sh
srcPath="cmd"
pkgFile="main.go"
app="gql-server"
src="$srcPath/$app/$pkgFile"

printf "\nStart running: $app\n"
# Set all ENV vars for the server to run
export $(grep -v '^#' .env | xargs) && time go run $src
# This should unset all the ENV vars, just in case.
unset $(grep -v '^#' .env | sed -E 's/(.*)=.*/\1/' | xargs)
printf "\nStopped running: $app\n\n" 
```

Enter fullscreen mode Exit fullscreen mode

这将设置环境变量和服务器。

你也可以添加一个`.gitignore`文件，运行`git init`将原点设置到你的
自己的库并`git push`它:)

这将在[第 2 部分](https://dev.to/cmelgarejo/creating-an-opinionated-graphql-server-with-go-part-2-46io)中继续，我们将添加服务器
的 GQLGen 部分！

所有的代码都可以在[这里](https://github.com/cmelgarejo/go-gql-server/tree/tutorial/part-1)找到
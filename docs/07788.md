# 带 Postgres 的基本 Go HTTP 服务器

> 原文：<https://dev.to/bmpickford/basic-go-http-server-with-postgres-og3>

我发现自己开始使用相同的基本设计创建简单的 Golang 微服务。在这篇文章中，我将从头开始讲述这些步骤，以帮助创建一个简单的起点，让 go 微服务或 API 能够创建客户

在这篇简短的教程中，我有意识地尽量少去了解原生包(net/http，encoding/json)

* * *

### 基本 HTTP 服务器

假设您已经完成了环境的所有设置，让我们首先在我们的新项目中用一个基本 http 服务器
创建一个`main.go`文件

```
package main

import (
    "log"
    "net/http"
)

const port = "8080"

func main() {
    log.Fatal(http.ListenAndServe(":8080", nil))
} 
```

Enter fullscreen mode Exit fullscreen mode

从这里开始，如果你运行`go run .`，你应该不会得到任何错误，应该是一个很好的指标，你的 go 设置是正确的

现在我们有了一个基本的服务器，是时候设置一个处理程序来检查我们的配置了。这里我们将为 GET /创建一个简单的响应。为此，将这个处理函数添加到`main.go` :

```
func handleHomePage(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello, the site is running :)")
} 
```

Enter fullscreen mode Exit fullscreen mode

然后这个到你的`main()`函数:

```
http.HandleFunc("/", homePage) 
```

Enter fullscreen mode Exit fullscreen mode

您的`main.go`文件现在应该看起来像:

```
package main

import (
    "fmt"
    "log"
    "net/http"
)

func main() {
    http.HandleFunc("/", handleHomePage)
    log.Fatal(http.ListenAndServe(":8080", nil))
}

func handleHomePage(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello, the site is running :)")
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，为了测试这一点，使用`curl -X GET 0.0.0.0:8080/`用 curl 点击服务器，您的响应应该是`Hello, the site is running :)`

* * *

### Postgres 设置

现在我们有了一个基本的 HTTP 服务器，是时候添加我们的 postgres DB 了。假设您已经安装了 postgres，让我们添加一个数据库和一个表。为此，在命令行中打开 postgres 并执行:

```
CREATE DATABASE go_project;
CREATE TABLE customer (id SERIAL, username VARCHAR NOT NULL); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### Go/Postgres 连接

为了设置我们的连接，我们将使用 golang 的 pq 包(使用`go get github.com/lib/pq`下载该包)。我们不会创建一个名为 postgres.go 的新文件，该文件将包含一个 singleton，我们将在后续文件中使用它来访问数据库。

```
package main

import (
    "database/sql"
    "log"

    _ "github.com/lib/pq"
)

var db *sql.DB

func GetDB() *sql.DB {
    var err error

    if db == nil {
        connStr := "user=postgres dbname=go_project"
        db, err = sql.Open("postgres", connStr)
        if err != nil {
            panic(err)
        }
    }

    return db
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里所做的，是使用我们数据库的名称和我们 postgres 实例的用户创建一个连接。这可能会根据您的设置而改变，并且可能需要一些身份验证

* * *

### 整理我们的经手人

现在是时候扩展我们的应用程序来包含一些真正的逻辑了。首先，我们必须创建我们的客户结构。首先，我们必须为我们的处理程序创建一个新文件。我已经创建了一个叫做`handler.go`的，但是你可以给它起任何你喜欢的名字。在这个文件中，添加包名和我们的新结构，如下所示:

```
package main

type Customer struct {
    ID       int
    Username string
} 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候添加我们的处理程序了。为此，我们需要创建一个解码器，将请求体(JSON 格式)解码成我们的结构。这可以使用下面的代码片段来完成:

```
func handleCustomerPost(w http.ResponseWriter, r *http.Request) {
    var customer Customer

    decoder := json.NewDecoder(r.Body)
    err := decoder.Decode(&customer)
    if err != nil {
        panic(err)
    }
    w.WriteHeader(201)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有能力将请求体解码成客户模型，是时候将我们的处理程序添加到`main.go`中了，就像我们对根响应所做的那样。但是这一次，我们必须过滤 POST 请求，因为本地 http 包没有这个功能。如果您添加 gorilla mux，这可以为您处理，但对于这种情况，我希望尽可能保持最小化。所以我们需要在 main 中添加另一个处理程序，如下:

```
func handleCustomerRequest(w http.ResponseWriter, r *http.Request) {
    if r.Method == http.MethodPost {
        handleCustomerRequest(w, r)
    }
    w.WriteHeader(201)
} 
```

Enter fullscreen mode Exit fullscreen mode

以及在`main()`函数中对它的调用如下:

```
http.HandleFunc("/customer", handleCustomerRequest) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 把这一切联系在一起

现在我们需要创建保存客户的查询。为此，我们需要创建一个新文件(我称之为 mine `database.go`)，并添加以下函数，该函数接受一个用户名

```
package main

func SaveCustomer(username string) error {
    db := GetDB()
    _, err := db.Exec("INSERT INTO customer (username) VALUES($1)", username)
    return err
} 
```

Enter fullscreen mode Exit fullscreen mode

现在在`handler.go`中，使用客户用户名调用这个函数，如下:

```
err = SaveCustomer(customer.Username)
if err != nil {
    panic(err)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候测试我们到目前为止的功能了。因此，再次使用`go run .`启动您的 go 服务。现在，如果您到达您的端点，您应该得到一个 201，并且在您的数据库中有一个带有 ID 和用户名的客户

### 结论

我希望你已经学会了用 golang 创建一个基本的 http 服务器。如果你想看完整的代码库，请看[https://github.com/bmpickford/goHttpServer](https://github.com/bmpickford/goHttpServer)
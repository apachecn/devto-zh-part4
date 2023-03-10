# 在 Go 的标准库中建立 Web 服务器进行 Hello，World

> 原文：<https://dev.to/shootacean/go-web-hello-world-1ogb>

利用 Go 的标准库`net/http`，可以简单地安装 Web 服务器。

```
# goファイルを作成する
$ touch server.go 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过编写以下代码来实现 Web 服务器:

```
package main

import (
    "fmt"
    "net/http"
)

func main() {
    // ルーティングを設定する
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello, World")
    })
    // ポート8080 番でサーバーを起動する
    if err := http.ListenAndServe(":8080", nil); err != nil {
        panic(err)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

执行以下命令后，通过浏览器等访问

[http://localhost:8080](http://localhost:8080)

，将输出 Hello、World。

```
$ go run server.go 
```

Enter fullscreen mode Exit fullscreen mode

用与执行上述命令时不同的终端会话，用 curl 命令尝试访问

```
$ curl localhost:8080
Hello, World 
```

Enter fullscreen mode Exit fullscreen mode

以上是用 Go 的标准库`net/http`构建 Web 服务器，进行 Hello，World 的步骤。
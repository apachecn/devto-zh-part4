# GoでのHello，世界

> 原文：<https://dev.to/shootacean/go-hello-world-2645>

从安装 Go 到输出 Hello，World 的步骤。

## 在 Mac 上安装 Go

```
$ brew install go
$ go version
go version go1.12.7 darwin/amd64 
```

Enter fullscreen mode Exit fullscreen mode

## Hello,Worldの実装

```
$ touch hello.go 
```

Enter fullscreen mode Exit fullscreen mode

```
package main

import "fmt"

func main() {
    fmt.Println("Hello, World")
} 
```

Enter fullscreen mode Exit fullscreen mode

## Hello，World 程序的执行

```
$ go run hello.go
Hello, World 
```

Enter fullscreen mode Exit fullscreen mode
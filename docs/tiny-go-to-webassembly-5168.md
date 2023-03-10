# (微小)转到 WebAssembly

> 原文：<https://dev.to/sendilkumarn/tiny-go-to-webassembly-5168>

地鼠不仅看起来可爱，而且它们很强大。

[![](img/8373d42f25b2fc76408bebffd1a6320f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_i4QVzEO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1eaxlpr98hv1bedjgyhg.gif)

I ❤️ [静态类型语言](https://en.wikipedia.org/wiki/Type_system)和 [Golang](https://golang.org/) 也是静态类型的。

> Golang 在语法上类似于 C，但是具有内存安全、垃圾收集和 CSP 风格的并发性。——[维基百科](https://en.wikipedia.org/wiki/Go_(programming_language))

我们可以把 Go 编译成 [WebAssembly](https://webassembly.org/) 。这意味着我们可以用 Golang 编写频道，并在浏览器上运行它们。

Golang 的 WebAssembly 仍处于早期阶段。🦄

Golang 写起来很简单。因此，很容易将现有应用程序直接编译到 WebAssembly 模块中，只要它们没有任何文件路径或系统级功能，而这些功能在 WebAssembly 运行时是不可用的。

# 你好世界

创建一个名为`go-wasm`的文件夹，子文件夹为`out`和`go`；

```
 go-wasm/
 |__ out/
 |__ go/ 
```

Enter fullscreen mode Exit fullscreen mode

在`go`文件夹中创建一个名为`main.go`的文件，并输入以下内容:

```
package main

func main() {
    println("Hello World!!!")
} 
```

Enter fullscreen mode Exit fullscreen mode

用`go run go/main.go`运行 go 文件。这会打印出`Hello World`。没那么容易。

现在我们可以将它编译成 WebAssembly 模块并作为 WebAssembly 运行。

```
GOOS=js GOARCH=wasm go build -o out/main.wasm go/main.go 
```

Enter fullscreen mode Exit fullscreen mode

这将在`out`文件夹中生成`main.wasm`。与 Rust 不同，Golang 不生成任何绑定文件。我们可以在这里使用 TinyGo 官方仓库[中的绑定文件。](https://github.com/tinygo-org/tinygo/blob/master/targets/wasm_exec.js)

下载文件并将其移动到`out`目录中。

在`out`目录下创建一个`index.html`文件，添加以下内容:

```
<!doctype html>
<html>
        <head>
                <meta charset="utf-8">
                Go wasm
        </head>

        <body>
                <script src="wasm_exec.js"></script>
                <script>
                        const go = new Go();
                        WebAssembly.instantiateStreaming(fetch('main.wasm'),go.importObject).then( res=> {
                                go.run(res.instance)    
                        })
                </script>

        </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

`index.html`加载`wasm_exec.js`文件。注意，这个文件适用于 Browser 和 NodeJS 环境。它导出一个`Go`对象。

然后我们添加一个本地脚本。在脚本中，我们执行以下操作:

*   从`wasm_exec.js`实例化`Go`。
*   使用[实例化流](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/instantiateStreaming)获取 WebAssembly 模块

从文件夹运行本地服务器查看`Hello World`。

但是等等，我们在写 Golang。Golang 使得用 Golang 编写一个简单的 web 服务器变得相当容易。让我们写一个。

在根目录下创建一个名为`webServer.go`的文件，内容如下。

```
package main

import (
        "log"
        "net/http"
        "strings"
)

const dir = "./out"

func main() {
        fs := http.FileServer(http.Dir(dir))
        log.Print("Serving " + dir + " on http://localhost:8080")
        http.ListenAndServe(":8080", http.HandlerFunc(func(resp http.ResponseWriter, req *http.Request) {
                resp.Header().Add("Cache-Control", "no-cache")
                if strings.HasSuffix(req.URL.Path, ".wasm") {
                        resp.Header().Set("content-type", "application/wasm")
                }
                fs.ServeHTTP(resp, req)
        }))
} 
```

Enter fullscreen mode Exit fullscreen mode

这服务于`out`文件夹中的文件。现在使用`go run webServer.go`运行服务器。🎉那不是很容易吗？

前往`http://localhost:8080`并打开控制台查看令人敬畏的`Hello World`。

目录结构应该如下所示。

```
├── go
│   └── main.go
└── out
    ├── index.html
    ├── wasm_exec.js
    └── main.wasm 
```

Enter fullscreen mode Exit fullscreen mode

记录生成的 WebAssembly 模块的文件大小非常重要。仅仅是《Hello World》就有 1.3 兆字节。这是巨大的权利。

```
-rw-r--r--  1 sendilkumar  staff   482B Jul  5 23:20 index.html
-rwxr-xr-x  1 sendilkumar  staff   1.3M Jul  5 23:19 main.wasm
-rw-r--r--  1 sendilkumar  staff    13K Jul  5 23:18 wasm_exec.js 
```

Enter fullscreen mode Exit fullscreen mode

WebAssembly 可能提供的任何性能，如果模块很大，就不需要它。

别担心，我们有一点点。

# 极小的围棋

[![](img/8e34603e522205662be1ffd5ee84701c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E8_855rx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2fz3lq3kohgoltymt7zq.png)

TinyGo 是一个将 Golang 引入微控制器和现代网络浏览器的项目。他们有一个全新的基于 LLVM 编译的编译器。使用 TinyGo，我们可以生成非常小的库，这些库被优化为在芯片中执行。

点击查看如何安装 TinyGo [。](https://tinygo.org/getting-started/)

安装后，您可以使用 TinyGo 编译任何 Golang 代码。我们可以使用以下命令将 Golang 编译成 WebAssembly 模块。

```
tinygo build -o out/main.wasm -target wasm ./go/main.go 
```

Enter fullscreen mode Exit fullscreen mode

现在转到浏览器，刷新页面，看看`Hello World`还在打印。

最重要的是生成的 WebAssembly 模块只有 3.8K🎉 🎉 🎉

```
-rw-r--r--  1 sendilkumar  staff   482B Jul  5 23:20 index.html
-rwxr-xr-x  1 sendilkumar  staff   3.8K Jul  5 23:29 main.wasm
-rw-r--r--  1 sendilkumar  staff    13K Jul  5 23:18 wasm_exec.js 
```

Enter fullscreen mode Exit fullscreen mode

因为我们下面有 LLVM，我们可以使用`-opt`标志进一步调整它。最大尺寸优化是通过-opt=z 标志获得的，默认情况下`TinyGo`使用这个标志。因为那些微型设备的内存有限。

在下一篇文章中继续关注，让我们使用 TinyGo 和 WebAssembly 重新创建经典 Dev 的离线页面。

[![](img/2a1d7d48b2ebfd44c03c733c79322d74.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mLl3vy45--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/18dmdedoviqs1vrrjqoo.png)

我希望这能给你一个动力，开始你的 WebAssembly 之旅。如果您有任何问题/建议/觉得我错过了什么，请随时添加评论。

你可以在推特上关注我。

如果你喜欢这篇文章，请留下赞或评论。❤️